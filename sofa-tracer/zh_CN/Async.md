## 异步线程处理

如果用户在代码中自己新启动了线程或者采用了线程池去异步地处理一些业务，那么需要将 SOFATracer 日志上下文从父线程传递到子线程中去，SOFATracer 提供的 `SofaTracerRunnable` 默认完成了此操作，大家可以按照如下的方式使用：

```java
Thread thread = new Thread(new SofaTracerRunnable(new Runnable() {
            @Override
            public void run() {
                //do something your business code
            }
        }));
thread.start();
```



