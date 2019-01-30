## Asynchronous thread processing

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

