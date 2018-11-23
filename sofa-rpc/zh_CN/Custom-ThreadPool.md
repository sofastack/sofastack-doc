## 自定义线程池

SOFARPC 支持自定义业务线程池。可以为指定服务设置一个独立的业务线程池，和 SOFARPC 自身的业务线程池是隔离的。多个服务可以共用一个独立的线程池。

SOFARPC 要求自定义线程池的类型必须是 `com.alipay.sofa.rpc.server.UserThreadPool`。

### XML 方式

如果采用 XML 的方式发布服务，可以先设定一个 class 为 `com.alipay.sofa.rpc.server.UserThreadPool` 的线程池的 Bean，然后设置到 `<sofa:global-attrs>` 标签的 `thread-pool-ref` 属性中：

```xml
<bean id="helloService" class="com.alipay.sofa.rpc.quickstart.HelloService"/>

<!-- 自定义一个线程池 -->
<bean id="customExecutor" class="com.alipay.sofa.rpc.server.UserThreadPool" init-method="init">
    <property name="corePoolSize" value="10" />
    <property name="maximumPoolSize" value="10" />
    <property name="queueSize" value="0" />
</bean>

<sofa:service ref="helloService" interface="XXXService">
    <sofa:binding.bolt>
        <!-- 将线程池设置给一个 Service -->
        <sofa:global-attrs thread-pool-ref="customExecutor"/>
    </sofa:binding.bolt>
</sofa:service>
```

### Annotation 方式

如果是采用 Annotation 的方式发布服务，可以通过设置 `@SofaServiceBinding` 的 `userThreadPool` 属性来设置自定义线程池的 Bean：

```java
@SofaService(bindings = {@SofaServiceBinding(bindingType = "bolt", userThreadPool = "customThreadPool")})
public class SampleServiceImpl implements SampleService {
}
```

### 在 Spring 环境使用 API 方式

如果是在 Spring 环境下使用 API 的方式发布服务，可以通过调用 `BoltBindingParam` 的 `setUserThreadPool` 方法来设置自定义线程池：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setUserThreadPool(new UserThreadPool());
```

### 在非 Spring 环境下使用 API 方式

如果是在非 Spring 环境下使用 API 的方式，可以通过如下的方式来设置自定义线程池：

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

如上为 HelloService 服务设置了一个自定义线程池。