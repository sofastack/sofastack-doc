## 自定义线程池

SOFARPC 支持自定义业务线程池。可以为指定服务设置一个独立的业务线程池，和 SOFARPC 自身的业务线程池是隔离的。多个服务可以共用一个独立的线程池。
API使用方式如下：
```java
UserThreadPool threadPool = new UserThreadPool();
threadPool.setCorePoolSize(10);
threadPool.setMaximumPoolSize(100);
threadPool.setKeepAliveTime(200);
threadPool.setPrestartAllCoreThreads(false);
threadPool.setAllowCoreThreadTimeOut(false);
threadPool.setQueueSize(200);

UserThreadPoolManager.registerUserThread("com.alipay.sofa.rpc.quickstart.HelloService", threadPool);
```
如上为 HelloService 服务设置了一个自定义线程池。

在 SOFABoot 中如下使用：
```xml
<bean id="customExcutor" class="com.alipay.sofa.rpc.server.UserThreadPool" init-method="init">
    <property name="corePoolSize" value="10" />
    <property name="maximumPoolSize" value="10" />
    <property name="queueSize" value="0" />
</bean>

<bean id="helloService" class="com.alipay.sofa.rpc.quickstart.HelloService"/>

<sofa:service ref="helloService" interface="XXXService">
    <sofa:binding.bolt>
        <sofa:global-attrs thread-pool-ref="customExcutor"/>
    </sofa:binding.bolt>
</sofa:service>
```