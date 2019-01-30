## Custom thread pool

SOFARPC supports custom business thread pools. A separate business thread pool can be set up for the specified service, isolated from SOFARPC's global business thread pool. Multiple services can share a single thread pool.

SOFARPC requires that the type of custom thread pool must be `com.alipay.sofa.rpc.server.UserThreadPool`.

### Use XML

If you publish the service using XML, you can first set the bean of the thread pool whose class is `com.alipay.sofa.rpc.server.UserThreadPool`, and then set the bean in the `thread-pool-ref` attribute of `<sofa:global-attrs>` tag.

```xml
<bean id="helloService" class="com.alipay.sofa.rpc.quickstart.HelloService"/>

<!-- Customize a thread pool -->
<bean id="customExecutor" class="com.alipay.sofa.rpc.server.UserThreadPool" init-method="init">
    <property name="corePoolSize" value="10" />
    <property name="maximumPoolSize" value="10" />
    <property name="queueSize" value="0" />
</bean>

<sofa:service ref="helloService" interface="XXXService">
    <sofa:binding.bolt>
        <!-- Set the thread pool to a Service -->
        <sofa:global-attrs thread-pool-ref="customExecutor"/>
    </sofa:binding.bolt>
</sofa:service>
```

### Use Annotation

If you publish the service using Annotation, you can set the bean of the custom thread pool by setting the `userThreadPool` attribute of `@SofaServiceBinding`:

```java
@SofaService(bindings = {@SofaServiceBinding(bindingType = "bolt", userThreadPool = "customThreadPool")})
public class SampleServiceImpl implements SampleService {
}
```

### Use API in Spring environment

If you publish a service using the API in Spring environment, you can configure a custom thread pool by calling the `setUserThreadPool` method of `BoltBindingParam`:

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setUserThreadPool(new UserThreadPool());
```

### Use API in non-Spring environment

If you publish service using the API in non-Spring environment, you can set a custom thread pool as follows:

```java
UserThreadPool threadPool = new UserThreadPool();
threadPool.setCorePoolSize(10);
threadPool.setMaximumPoolSize(100);
threadPool.setKeepAliveTime(200);
threadPool.setPrestartAllCoreThreads(false);
threadPool.setAllowCoreThreadTimeOut(false);
threadPool.setQueueSize(200);

UserThreadPoolManager.registerUserThread(ConfigUniqueNameGenerator.getUniqueName(providerConfig), threadPool);
```

As above, a custom thread pool is set up for the HelloService service.