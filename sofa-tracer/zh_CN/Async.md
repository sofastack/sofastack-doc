## 异步线程处理

### 线程中使用 `java.lang.Runnable`

如果用户在代码中通过 `java.lang.Runnable` 新启动了线程或者采用了线程池去异步地处理一些业务，那么需要将 SOFATracer 日志上下文从父线程传递到子线程中去，SOFATracer 提供的 `com.alipay.common.tracer.core.async.SofaTracerRunnable` 默认完成了此操作，大家可以按照如下的方式使用：

```java
Thread thread = new Thread(new SofaTracerRunnable(new Runnable() {
            @Override
            public void run() {
                //do something your business code
            }
        }));
thread.start();
```

### 线程中使用 `java.util.concurrent.Callable`

如果用户在代码中通过 `java.util.concurrent.Callable` 新启动线程或者采用了线程池去异步地处理一些业务，那么需要将 SOFATracer 日志上下文从父线程传递到子线程中去，SOFATracer 提供的 `com.alipay.common.tracer.core.async.SofaTracerCallable` 默认完成了此操作，大家可以按照如下的方式使用：

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

这个实例中，假设 `java.util.concurrent.Callable` 返回结果的对象类型是 `java.lang.Object`，实际使用时可以根据情况替换为期望的类型。


