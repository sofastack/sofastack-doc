# Asynchronous thread processing

### Use `java.lang.Runnable` in thread

If you start a thread via `java.lang.Runnable` in the code or use a thread pool to process some businesses asynchronously, SOFATracer log context needs to be passed from the parent thread to the child thread. `com.alipay.common.tracer.core.async.SofaTracerRunnable` provided by SOFATracer is reponsible for completing this operation by default. You can use it as follows:

```java
Thread thread = new Thread(new SofaTracerRunnable(new Runnable() {
            @Override
            public void run() {
                //do something your business code
            }
        }));
thread.start();
```

### Use `java.util.concurrent.Callable` in thread

If you start a thread via `java.util.concurrent.Callable` in the code or use a thread pool to process some businesses asynchronously, SOFATracer log context needs to be passed from the parent thread to the child thread. `com.alipay.common.tracer.core.async.SofaTracerCallable` provided by SOFATracer  is reponsible for completing this operation by default. You can use it as follows:

```java
 ExecutorService executor = Executors.newCachedThreadPool();
 SofaTracerCallable<Object> sofaTracerSpanSofaTracerCallable = new SofaTracerCallable<Object>(new Callable<Object>() {
     @Override
     public Object call() throws Exception {
         return new Object();
     }
 });
 Future<Object> futureResult = executor.submit(sofaTracerSpanSofaTracerCallable);
 //do something in current thread
 Thread.sleep(1000);
 //another thread execute success and get result
 Object objectReturn = futureResult.get();
```

This example assumes that the object type returned by `java.util.concurrent.Callable` is `java.lang.Object`. You can replace it with the expected type based on actual situation.

### SOFATracer support for thread pool and asynchronous call scenarios

#### Asynchronous

> Asynchronous invocation, in RPC calls, for example,each time the rpc call request goes out, it will not wait until the result is returned before initiating the next call. There is a time difference here, before the callback of the previous rpc call comes back, another new one begin. At this time, the TracerContext in the current thread is not cleaned up, the spanId will be incremented, and the tracerId is the same.

For the above situation, when the SOFATracer is processed for the asynchronous situation, it will not wait for the callback to execute, and then the cr phase will be cleaned up. Instead, the current thread's tracerContext context will be cleaned up in advance to ensure the correctness of the link.

#### Thread Pool

For now, whether it's SOFARPC or Dubbo's trace implementation, the situation is the same when using single-thread or thread pools:

* Synchronous call. A thread in the thread pool is allocated to handle the RPC request. This does not cause the next RPC request to take the tracerContext data of the previous request by mistake
* Asynchronous calls, since the asynchronous callback is not in the callback to clean up the context, but in advance, there is no dirty data problem.
* callback, which is essentially an asynchronous call, so the situation is the same as the asynchronous call.

Attached: [Case Project](https://github.com/glmapper/sofa-tracer-concurrence-parent)
