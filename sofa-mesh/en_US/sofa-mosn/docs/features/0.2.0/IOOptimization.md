# 0.2.0 Goroutine optimization

# Overall solution
- Use the epoll + event-driven programming model instead of Golang's default epoll + goroutine blocking programming model on the underlayer.
- Implement IO goroutine pooling to reduce the goroutine scheduling overhead and memory consumption in the scenario where a large number of connections exist.
- Configure switches. You can decide to use which goroutine models according to your own actual scenarios.

# Detailed changes
When connection starts reading and writing, if the current mode is netpoll, the system adds its own readable events and the corresponding processing functions to the global epoll wait event loop.

Sample codes:

```go
//create poller
poller, err := netpoll.New(nil)
if err != nil {
    // handle error
}

// register read/write events instead of starting I/O goroutine
read, _ := netpoll.HandleReadOnce(c.RawConn())
poller.Start(read, func(e netpoll.Event) {
            if e&netpoll.EventReadHup != 0 {
            (*poller).Stop(read)
             // process hup
                     ...
            return
            }

        // Read logic
            go doRead()

        // Enable read event again
        poller.Resume(read)
    })
})
```

When the connection is closed, if the connection runs in netpoll mode, you must unregister its registered read and write events from the event loop.

Sample codes:

```go
        // wait for io loops exit, ensure single thread operate streams on the connection
    if c.internalLoopStarted {
        // because close function must be called by one io loop thread, notify another loop here
        close(c.internalStopChan)
    } else if c.eventLoop != nil {
        c.eventLoop.Unregister(c.id)
    }
```


## Implement IO goroutine pooling
When a readable event is triggered, the system fetches a goroutine from the goroutine pool to perform the read processing instead of allocating a goroutine. This is used to control the number of goroutines under high concurrency.

Sample codes:
```go
//create poller
poller, err := netpoll.New(nil)
if err != nil {
    // handle error
}

// register read/write events instead of starting I/O goroutine
read, _ := netpoll.HandleReadOnce(c.RawConn())
poller.Start(read, func(e netpoll.Event) {
    // No more calls will be made for conn until we call epoll.Resume().
    if e&netpoll.EventReadHup != 0 {
        (*poller).Stop(read)
         // process hup
                 ...
        return
    }
    
    // Use worker pool to process read event
    pool.Schedule(func() {
        // Read logic
        doRead()

        // Enable read event again
        poller.Resume(read)
    })
})
```


**Note**: Since `conn.write` may block goroutines, SOFAMosn has adopted a looser strategy for the pooling of write operations. Namely, the number of pooled resident goroutines keeps fixed, but new temporary goroutines are allowed. 


Pooling codes:

```go
type SimplePool struct {
    work chan func()
    sem chan struct{}
}


func NewSimplePool(size int) *SimplePool {
    return &SimplePool{
        work: make(chan func()),
        sem:  make(chan struct{}, size),
    }
}

func (p *SimplePool) Schedule(task func()) {
    select {
    case p.work <- task:
    case p.sem <- struct{}{}:
        go p.worker(task)
    }
}

// for write schedule
func (p *SimplePool) ScheduleAlways(task func()) {
    select {
    case p.work <- task:
    case p.sem <- struct{}{}:
        go p.worker(task)
    default:
        go task()
    }
}

func (p *SimplePool) worker(task func()) {
    defer func() { <-p.sem }()
    for {
        task()
        task = <-p.work
    }
}
```
## Configure switches
Add a global configuration to control the read/write goroutine operation mode of the connections: netpoll+ goroutine pool or connection-level read/write goroutine.

Sample codes:

```go
func (c *connection) Start(lctx context.Context) {
    c.startOnce.Do(func() {
        if UseNetpollMode {
            c.attachEventLoop(lctx)
        } else {
            c.startRWLoop(lctx)
        }
    })
}
```

Sample configuration:

```json
"servers": [
        {
       "use_netpoll_mode": true,
    }
]
```
# Performance comparison
## Goroutine 

| 10,000 persistent connections | Before optimization | After optimization |
| -------- | -------- | -------- |
| goroutine  |  20040  | 40    |

## Memory usage
Save the memory of goroutine stack.

| 10,000 persistent connections | Before optimization | After optimization |
| -------- | -------- | -------- |
| stack     |   117M   | 1M     |

## Performance in the scenario where a large number of connections exist

> 10,000 persistent connections, 300qps

|  | Before optimization  | After optimization |
| -------- | -------- | -------- |
| CPU     |  14%    | 8%     |

> 10,000 persistent connections, 3000qps

|  | Before optimization | After optimization |
| -------- | -------- | -------- |
| CPU     |    52%  |  40%   |

> 10,000 persistent connections, 10000qps

|  | Before optimization | After optimization |
| -------- | -------- | -------- |
| CPU     |   90%   |   91%   |

## Performance in pressure test

| Indicators | IO goroutine | netpoll |
| -------- | -------- | -------- |
| QPS     |   23000   |   22000   |
| CPU     |   100%   |   100%   |
| RT     |   9ms   |   10ms   |

## Analysis
For the scenario where inactive connections are in majority, the optimization can effectively reduce the number of goroutines, memory usage, and CPU usage. For the scenario where active connections are in majority or there are few connections but a lot of requests, there may be slight performance loss.


Therefore, SOFAMosn provides an optional configuration `UseNetpollMode`. You can select the goroutine model based on your own actual situation.
