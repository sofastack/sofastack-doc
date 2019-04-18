# 异步线程处理

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

### SOFATracer 对线程池、异步调用场景下的支持

#### 异步场景

> 异步调用，以 rpc 调用为例，每次 rpc 调用请求出去之后不会等待到结果返回之后才去发起下一次处理，这里有个时间差，在前一个 rpc 调用的 callback 回来之前，又一个新的 rpc 请求发起，此时当前线程中的 TracerContext 没有被清理，则 spanId 会自增，tracerId 相同。

对于上面这种情况，SOFATracer 在对于异步情况处理时，不会等到 callback 回来之后，调用 cr 阶段才会清理，而是提前就会清理当前线程的 tracerContext 上下文，从而来保证链路的正确性。

#### 线程池

目前来说，不管是 SOFARPC 还是 Dubbo 的埋点实现，在使用单线程或者线程池时，情况是一样的：

* 同步调用，线程池中分配一个线程用于处理 rpc 请求，在请求结束之前会一直占用线程；此种情况下不会造成下一个 rpc 请求错拿上一个请求的 tracerContext 数据问题
* 异步调用，由于异步回调并非是在 callback 中来清理上下文，而是提前清理的，所以也不会存在数据串用问题。
* callback 异步回调，这个本质上就是异步调用，所以处理情况和异步调用相同。

附：[案例工程](https://github.com/glmapper/sofa-tracer-concurrence-parent)