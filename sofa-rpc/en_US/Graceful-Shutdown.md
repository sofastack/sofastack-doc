## Graceful shutdown

Graceful shutdown includes two parts. One is the RPC framework as client, and the other is the RPC framework as server.


### As server

As the server, the RPC framework should not be violently shutdown.

```java
com.alipay.sofa.rpc.context.RpcRuntimeContext

```

Added a `ShutdownHook` to the static initialization snippet:

```java
// Add jvm shutdown event
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

The logic in `ShutdownHook` is executed first when the publishing platform or users run the following method `kill pid`. In the `destroy` operation, the RPC framework first performs actions such as canceling service registration to the registry center and closing the service port.

```java
private static void destroy(boolean active) {
        RpcRunningState.setShuttingDown (true);
        for (Destroyable.DestroyHook destroyHook : DESTROY_HOOKS) {
            destroyHook.preDestroy();
        }
        List<ProviderConfig> providerConfigs = new ArrayList<ProviderConfig>();
        for (ProviderBootstrap bootstrap : EXPORTED_PROVIDER_CONFIGS) {
            providerConfigs.add(bootstrap.getProviderConfig());
        }
        // First, unregister the server
        List<Registry> registries = RegistryFactory.getRegistries();
        if (CommonUtils.isNotEmpty(registries) && CommonUtils.isNotEmpty(providerConfigs)) {
            for (Registry registry : registries) {
                registry.batchUnRegister(providerConfigs);
            }
        }
        / / Shut down the port that has been started
        ServerFactory.destroyAll();
        // Close the published service
        for (ProviderBootstrap bootstrap : EXPORTED_PROVIDER_CONFIGS) {
            bootstrap.unExport();
        }
        // Close the called service
        for (ConsumerBootstrap bootstrap : REFERRED_CONSUMER_CONFIGS) {
            ConsumerConfig config = bootstrap.getConsumerConfig();
            If (!CommonUtils.isFalse(config.getParameter(RpcConstants.HIDDEN_KEY_DESTROY))) { // Unless you do not let the active unrefer
                bootstrap.unRefer();
            }
        }
        // Shut down the registry center
        RegistryFactory.destroyAll();
        / / Close some public resources of the client
        ClientTransportFactory.closeAll();
        // Uninstall the module
        if (!RpcRunningState.isUnitTestMode()) {
            ModuleFactory.uninstallModules();
        }
        // Uninstall the hook
        for (Destroyable.DestroyHook destroyHook : DESTROY_HOOKS) {
            destroyHook.postDestroy();
        }
        // Clean up the cache
        RpcCacheManager.clearAll();
        RpcRunningState.setShuttingDown (false);
        if (LOGGER.isWarnEnabled()) {
            LOGGER.warn("SOFA RPC Framework has been release all resources {}...",
                active ? "actively " : "");
        }
    }
```

Taking bolt as an example, closing the port is not an immediate action.

```java
    @Override
    public void destroy() {
        if (!started) {
            return;
        }
        int stopTimeout = serverConfig.getStopTimeout();
        If (stopTimeout > 0) { // need to wait for the end time
            AtomicInteger count = boltServerProcessor.processingCount;
            // There are requests being executed or there are requests in the queue
            if (count.get() > 0 || bizThreadPool.getQueue().size() > 0) {
                long start = RpcRuntimeContext.now();
                if (LOGGER.isInfoEnabled()) {
                    LOGGER.info("There are {} call in processing and {} call in queue, wait {} ms to end",
                        count, bizThreadPool.getQueue().size(), stopTimeout);
                }
                while ((count.get() > 0 || bizThreadPool.getQueue().size() > 0)
                    && RpcRuntimeContext.now() - start < stopTimeout) { // Wait for the result
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException ignore) {
                    }
                }
            } // Check for existing requests before shutdown?
        }

        / / Shut down the thread pool
        bizThreadPool.shutdown();
        stop();
    }
```

Instead, it will determine the tasks of the current connection and queue on the server, first process the tasks in the queue, and then slowly shutdown.


### As client

As the client, RPC framework shutdown is actually the shutdown of the cluster. About the step of closing the called service, you can use the following code:

```java
com.alipay.sofa.rpc.client.AbstractCluster
```

```java
    /**
     * Hooks that are shut down gracefully
     */
    protected class GracefulDestroyHook implements DestroyHook {
        @Override
        public void preDestroy() {
            // ready to close the connection
            int count = countOfInvoke.get();
            Final int timeout = consumerConfig.getDisconnectTimeout(); // wait for result timeout
            If (count > 0) { // there is a request being called
                long start = RpcRuntimeContext.now();
                if (LOGGER.isWarnEnabled()) {
                    LOGGER.warn("There are {} outstanding call in client, will close transports util return",
                        count);
                }
                While (countOfInvoke.get() > 0 && RpcRuntimeContext.now() - start < timeout) { // Wait for the result
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

In this implementation, service will go offline only after all the requests are processed.

## Best practices

It can be seen that graceful shutdown is required to be linked to publishing platform. If you force kill, any graceful shutdown solution will not take effect. In the future, we may consider providing a unified API at the SOFABoot level for the publishing platform, rather than relying on hook logic.