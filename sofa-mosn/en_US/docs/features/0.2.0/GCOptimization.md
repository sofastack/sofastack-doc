# 0.2.0 GC and memory usage optimization

## Overall solution

+ Add a memory reuse framework which can easily reuse custom memory.
+ Improve BytePool to facilitate global reuse.
+ Improve IoBufferPool, and use BytePool on the underlayer to enhance memory reuse.
+ Improve Read/Write Buffer to reduce memory usage and enhance performance.
+ Optimize SOFA implementation to reduce the generation of temporary objects.
+ Reuse the frequently-used memory structure on the protocol, stream, and proxy layers.

## Detailed changes

### Memory reuse framework
Implement the following interfaces to make it more convenient to reuse the required memory.

+ Name(): The unique identifier for returning the custom memory structure
+ New(): The method for creating a new memory structure
+ Reset(): The method for resetting the memory structure, which is used to call and clear the structure before retrieving.

```go
// BufferPoolCtx is the bufferpool's context
type BufferPoolCtx interface {
    // Name returns the bufferpool's name
    Name() int

    // New returns the buffer
    New() interface{}

    // Reset resets the buffer
    Reset(interface{})
}
```

Take SOFA for example:

+ `SofaProtocolBufferCtx` implements the `BufferPoolCtx` interface.
+ `SofaProtocolBuffers` is the memory structure to be reused.
+ `SofaProtocolBuffersByContent` method is used to get the memory structure.
+ The memory structure is released uniformly after the request ends, and the request context is correlated through `context`.

```go
type SofaProtocolBufferCtx struct{}

func (ctx SofaProtocolBufferCtx) Name() int {
    return buffer.SofaProtocol
}

func (ctx SofaProtocolBufferCtx) New() interface{} {
    buffer := new(SofaProtocolBuffers)
    return buffer
}

func (ctx SofaProtocolBufferCtx) Reset(i interface{}) {
    buf, _ := i.(*SofaProtocolBuffers)
    buf.BoltReq = BoltRequestCommand{}
    buf.BoltRsp = BoltResponseCommand{}
    buf.BoltEncodeReq = BoltRequestCommand{}
    buf.BoltEncodeRsp = BoltResponseCommand{}
}

type SofaProtocolBuffers struct {
    BoltReq       BoltRequestCommand
    BoltRsp       BoltResponseCommand
    BoltEncodeReq BoltRequestCommand
    BoltEncodeRsp BoltResponseCommand
}

func SofaProtocolBuffersByContent(context context.Context) *SofaProtocolBuffers {
    ctx := buffer.PoolContext(context)
    return ctx.Find(SofaProtocolBufferCtx{}, nil).(*SofaProtocolBuffers)
}
```

How to use:

```go
sofabuffers := sofarpc.SofaProtocolBuffersByContent(context)
request := &sofabuffers.BoltReq
```

### Improve BytePool
Add `GetBytes()` and `PutBytes()` functions to globally fetch and reclaim `*[]byte` with the pool implementation details masked.
 
```go
// GetBytes returns *[]byte from byteBufferPool
func GetBytes(size int) *[]byte 

// PutBytes Put *[]byte to byteBufferPool
func PutBytes(buf *[]byte) 
```

### Improve IoBuffer
+ Add `GetIoBuffer()` and `PutIoBuffer()` functions to globally fetch and release `types.IoBuffer` with the pool implementation details masked.

    ```go
    // GetIoBuffer returns IoBuffer from pool
    func GetIoBuffer(size int) types.IoBuffer
    
    // PutIoBuffer returns IoBuffer to pool
    func PutIoBuffer(buf types.IoBuffer)
    ```

+ The `[]byte` encapsulated by Iobuffer is fetched by using `GetBytes()`.

    ```go
    func NewIoBuffer(capacity int) types.IoBuffer {
        buffer := &IoBuffer{
            offMark: ResetOffMark,
        }
        if capacity <= 0 {
            capacity = DefaultSize
        }
        buffer.b = GetBytes(capacity)
        buffer.buf = (*buffer.b)[:0]
        return buffer
    }
    ```

+ Add the method `Free()` to release `[]byte`. `Alloc()` is used to allocate `[]byte`.

    ```go
    func (b *IoBuffer) Free() 
    
    func (b *IoBuffer) Alloc(size int)
    ```

### Read/Write buffer
#### Read buffer
1. Read circularly until the buffer is unreadable or reaches the preset maximum value. The expansion of IoBuffer uses the `GetBytes()` and `PutBytes()` methods to improve memory reuse.
2. If a read timeout occurs, you can call `Free()` to release `[]byte`, and then call `Alloc()` to allocate a 1-byte `[]byte` which is used to wait for the read event and reduce the memory usage of idle connection.


  ```go
    if te, ok := err.(net.Error); ok && te.Timeout() {
        if c.readBuffer != nil && c.readBuffer.Len() == 0 {
            c.readBuffer.Free()
            c.readBuffer.Alloc(DefaultBufferReadCapacity)
        }
        continue
    }    
  ```
 
#### Write buffer

Optimize the original `write` call to `writev`, thus reducing the memory allocation and copy and reducing lock strength.

 1. `write` passes the slice pointer of IoBuffer to WriteLoop through `chan`.
 
     ```go
     func (c *connection) Write(buffers ...types.IoBuffer) error {
    
        c.writeBufferChan <- &buffers
    
        return nil
    }
     ```

 2. WriteLoop merges IoBuffer, and then calls `doWriteIo`.

     ```go
     func (c *connection) startWriteLoop() {
            select {
            case buf := <-c.writeBufferChan:
                c.appendBuffer(buf)
    
                //todo: dynamic set loop nums
                for i := 0; i < 10; i++ {
                    select {
                    case buf := <-c.writeBufferChan:
                        c.appendBuffer(buf)
                    default:
                    }
                }
                _, err = c.doWriteIo()
            }
     ```

 3. Call the `WriteTo` method of `net.Buffers`. Use the writev system to call once to send multiple IoBuffer.
 4. Finally, call `PutIoBuffer()` to release the sent IoBuffer.
 
 ```go
func (c *connection) doWriteIo() (bytesSent int64, err error) {
    bytesSent, err = c.writeBuffers.WriteTo(c.rawConnection)
    if err != nil {
        return bytesSent, err
    }
    for _, buf := range c.ioBuffers {
        buffer.PutIoBuffer(buf)
    }
    c.ioBuffers = c.ioBuffers[:0]
    return
}
 ```

* `buffersWriter` interface

```go
// buffersWriter is the interface implemented by Conns that support a
// "writev"-like batch write optimization.
// writeBuffers should fully consume and write all chunks from the
// provided Buffers, else it should report a non-nil error.
type buffersWriter interface {
    writeBuffers(*Buffers) (int64, error)
}
```
### Optimize SOFA
* Use `SofaProtocolBuffers` to reuse the structure body, as well as IoBuffer, map and other structures.
* Reduce temporary objects <br>  
   The return value of `ConvertPropertyValue` is `interface{}` which may generate temporary objects. This method is frequently called in the parsing process. You can use `ConvertPropertyValueUint8` and other functions to avoid the generation of temporary objects.

 ```go
 func ConvertPropertyValue(strValue string, kind reflect.Kind) interface{} {
    switch kind {
    case reflect.Uint8:
        value, _ := strconv.ParseUint(strValue, 10, 8)
        return byte(value)
    case reflect.Uint16:
        value, _ := strconv.ParseUint(strValue, 10, 16)
        return uint16(value)
 }    
 
 func ConvertPropertyValueUint8(strValue string) byte {
    value, _ := strconv.ParseUint(strValue, 10, 8)
    return byte(value)
 }

 func ConvertPropertyValueUint16(strValue string) uint16 {
    value, _ := strconv.ParseUint(strValue, 10, 16)
    return uint16(value)
 }
 ```

## Test
### Unit test
Test codes: buffer_test.go
* Bench (the scenario is just for your reference)

```go
goos: darwin
goarch: amd64
pkg: github.com/alipay/sofa-mosn/pkg/buffer
// Use BytePool to allocate
1000000          1762 ns/op          26 B/op           0 allocs/op
// Allocate []byte directly
1000000          2086 ns/op        2048 B/op           1 allocs/op
// Use IobufferPool allocate
1000000          1225 ns/op          31 B/op           0 allocs/op
// Allocate Iobuffer directly
1000000          1585 ns/op        2133 B/op           3 allocs/op
PASS
```
