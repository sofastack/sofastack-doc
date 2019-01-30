## 优雅关闭

优雅关闭，包括两部分，一个是 RPC 框架作为客户端，一个是 RPC 框架作为服务端。


### 作为服务端

作为服务端的时候，RPC 框架在关闭时，不应该直接暴力关闭。在 RPC 框架中

```java
com.alipay.sofa.rpc.context.RpcRuntimeContext

```

在静态初始化块中，添加了一个 ShutdownHook

```java
  // 增加jvm关闭事件
        if (RpcConfigs.getOrDefaultValue(RpcOptions.JVM_SHUTDOWN_HOOK, true)) {
            Runtime.getRuntime().addShutdownHook(new Thread(new Runnable() {
                @Override
                public void run() {
                    if (LOGGER.isWarnEnabled()) {
                        LOGGER.warn("SOFA RPC Framework catch JVM shutdown event, Run shutdown hook now.");
                    }
                    destroy(false);
                }
            }, "SOFA-RPC-ShutdownHook"));
        }
```

这个 ShutdownHook 的作用是当发布平台/用户执行 `kill pid` 的时候，会先执行 ShutdownHook 中的逻辑。在销毁操作中，RPC 框架会先执行向注册中心取消服务注册、关闭服务端口等动作。


```java
private static void destroy(boolean active) {
        RpcRunningState.setShuttingDown(true);
        for (Destroyable.DestroyHook destroyHook : DESTROY_HOOKS) {
            destroyHook.preDestroy();
        }
        List<ProviderConfig> providerConfigs = new ArrayList<ProviderConfig>();
        for (ProviderBootstrap bootstrap : EXPORTED_PROVIDER_CONFIGS) {
            providerConfigs.add(bootstrap.getProviderConfig());
        }
        // 先反注册服务端
        List<Registry> registries = RegistryFactory.getRegistries();
        if (CommonUtils.isNotEmpty(registries) && CommonUtils.isNotEmpty(providerConfigs)) {
            for (Registry registry : registries) {
                registry.batchUnRegister(providerConfigs);
            }
        }
        // 关闭启动的端口
        ServerFactory.destroyAll();
        // 关闭发布的服务
        for (ProviderBootstrap bootstrap : EXPORTED_PROVIDER_CONFIGS) {
            bootstrap.unExport();
        }
        // 关闭调用的服务
        for (ConsumerBootstrap bootstrap : REFERRED_CONSUMER_CONFIGS) {
            ConsumerConfig config = bootstrap.getConsumerConfig();
            if (!CommonUtils.isFalse(config.getParameter(RpcConstants.HIDDEN_KEY_DESTROY))) { // 除非不让主动unrefer
                bootstrap.unRefer();
            }
        }
        // 关闭注册中心
        RegistryFactory.destroyAll();
        // 关闭客户端的一些公共资源
        ClientTransportFactory.closeAll();
        // 卸载模块
        if (!RpcRunningState.isUnitTestMode()) {
            ModuleFactory.uninstallModules();
        }
        // 卸载钩子
        for (Destroyable.DestroyHook destroyHook : DESTROY_HOOKS) {
            destroyHook.postDestroy();
        }
        // 清理缓存
        RpcCacheManager.clearAll();
        RpcRunningState.setShuttingDown(false);
        if (LOGGER.isWarnEnabled()) {
            LOGGER.warn("SOFA RPC Framework has been release all resources {}...",
                active ? "actively " : "");
        }
    }
```

其中以 bolt 为例，关闭端口并不是一个立刻执行的动作

```java
 @Override
    public void destroy() {
        if (!started) {
            return;
        }
        int stopTimeout = serverConfig.getStopTimeout();
        if (stopTimeout > 0) { // 需要等待结束时间
            AtomicInteger count = boltServerProcessor.processingCount;
            // 有正在执行的请求 或者 队列里有请求
            if (count.get() > 0 || bizThreadPool.getQueue().size() > 0) {
                long start = RpcRuntimeContext.now();
                if (LOGGER.isInfoEnabled()) {
                    LOGGER.info("There are {} call in processing and {} call in queue, wait {} ms to end",
                        count, bizThreadPool.getQueue().size(), stopTimeout);
                }
                while ((count.get() > 0 || bizThreadPool.getQueue().size() > 0)
                    && RpcRuntimeContext.now() - start < stopTimeout) { // 等待返回结果
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException ignore) {
                    }
                }
            } // 关闭前检查已有请求？
        }

        // 关闭线程池
        bizThreadPool.shutdown();
        stop();
    }
```

而是会判断当前服务端上面的连接和队列的任务，先处理完队列中的任务，再缓慢关闭。


### 作为客户端

作为客户端。实际上就是 Cluster 的关闭，关闭调用的服务这一步，可以查看下

```java
com.alipay.sofa.rpc.client.AbstractCluster
```

```java
/**
     * 优雅关闭的钩子
     */
    protected class GracefulDestroyHook implements DestroyHook {
        @Override
        public void preDestroy() {
            // 准备关闭连接
            int count = countOfInvoke.get();
            final int timeout = consumerConfig.getDisconnectTimeout(); // 等待结果超时时间
            if (count > 0) { // 有正在调用的请求
                long start = RpcRuntimeContext.now();
                if (LOGGER.isWarnEnabled()) {
                    LOGGER.warn("There are {} outstanding call in client, will close transports util return",
                        count);
                }
                while (countOfInvoke.get() > 0 && RpcRuntimeContext.now() - start < timeout) { // 等待返回结果
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException ignore) {
                    }
                }
            }
        }

        @Override
        public void postDestroy() {
        }
    }
```

这里面也会逐步将正在调用的请求处理完成才会下线。


## 最佳实践

可以看到，优雅关闭是需要和发布平台联动的。如果强制 kill，那么任何优雅关闭的方案都不会生效。后续我们会考虑在 SOFABoot 层面提供一个统一的 API， 来给发布平台调用。而不是依赖 hook 的逻辑。