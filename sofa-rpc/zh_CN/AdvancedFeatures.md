# 高级特性

其中关于 SOFABoot 环境下的使用方式具体可见 [sofa-boot-starter 用户手册](https://github.com/alipay/sofa-rpc-boot-projects/wiki/UserGuide)

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
## 链路数据透传
链路数据透传功能支持应用向调用上下文中存放数据，达到整个链路上的应用都可以操作该数据。
使用方式如下，可分别向链路的 request 和 response 中放入数据进行透传，并可获取到链路中相应的数据。
```java
RpcInvokeContext.getContext().putRequestBaggage("key_request","value_request");
RpcInvokeContext.getContext().putResponseBaggage("key_response","value_response");

RpcInvokeContext.getContext().getAllRequestBaggage("key_request");
RpcInvokeContext.getContext().getAllRequestBaggage("key_response");
```

## 预热权重
预热权重功能让客户端机器能够根据服务端的相应权重进行流量的分发。该功能也常被用于集群内少数机器的启动场景。利用流量权重功能在短时间内对服务端机器进行预热，然后再接收正常的流量比重。
运行机制如下：
![[预热权重示意图]()](./resources/ug_1.png)

1.服务端服务在启动时会将自身的预热时间，预热期内权重，预热完成后的正常权重推送给服务注册中心。如上图 ServiceB 指向 Service Registry 。

2.客户端在引用服务的时候会获得每个服务实例的预热权重信息。如上图 Service Registry 指向 client 。

3.客户端在进行调用的时候会根据服务所在地址的预热时期所对应的权重进行流量分发。如上图 client 指向 ServiceA 和 ServiceB 。 ServiceA 预热完毕，权重默认 100 ， ServiceB 处于预热期，权重为 10，因此所承受流量分别为 100%110 和 10%110 。

该功能使用方式如下。
```java
ProviderConfig<HelloWordService> providerConfig = new ProviderConfig<HelloWordService>() 
            .setWeight(100) 
            .setParameter(ProviderInfoAttrs.ATTR_WARMUP_WEIGHT,"10") 
            .setParameter(ProviderInfoAttrs.ATTR_WARM_UP_END_TIME,"12000");
```
如上，该服务的预热期为12s，在预热期内权重为10，预热期结束后的正常权重为100。如果该服务一共发布在两个机器A,B上，A机器正处于预热期内，并使用上述配置，B已经完成预热，正常权重为200。那么客户端在调用的时候，此时流量分发的比重为10：200，A机器预热结束后，流量分发比重为100：200。
在SOFABoot中，如下配置预热时间，预热期间权重和预热完后的权重即可。
```java
<sofa:reference id="sampleRestFacadeReferenceBolt" interface="com.alipay.sofa.endpoint.facade.SampleFacade">
    <sofa:binding.bolt>
         <sofa:global-attrs weight="100" warm-up-time="10000" warm-up-weight="1000"/>
     </sofa:binding.bolt>
</sofa:reference>
```
## 自动故障剔除
   集群中通常一个服务有多个服务提供者。其中部分服务提供者可能由于网络，配置，长时间 fullgc ，线程池满，硬件故障等导致长连接还存活但是程序已经无法正常响应。单机故障剔除功能会将这部分异常的服务提供者进行降级，使得客户端的请求更多地指向健康节点。当异常节点的表现正常后，单机故障剔除功能会对该节点进行恢复，使得客户端请求逐渐将流量分发到该节点。单机故障剔除功能解决了服务故障持续影响业务的问题，避免了雪崩效应。可以减少人工干预需要的较长的响应时间，提高系统可用率。

   运行机制：
* 单机故障剔除会统计一个时间窗口内的调用次数和异常次数，并计算每个服务对应ip的异常率和该服务的平均异常率。
* 当达到ip异常率大于服务平均异常率到一定比例时，会对该服务+ip的维度进行权重降级。
* 如果该服务+ip维度的权重并没有降为0，那么当该服务+ip维度的调用情况正常时，则会对其进行权重恢复。
* 整个计算和调控过程异步进行，不会阻塞调用。

单机故障剔除的使用方式如下：
```java
FaultToleranceConfig faultToleranceConfig = new FaultToleranceConfig();
        faultToleranceConfig.setRegulationEffective(true);
        faultToleranceConfig.setDegradeEffective(true);
        faultToleranceConfig.setTimeWindow(20);
        faultToleranceConfig.setWeightDegradeRate(0.5);

FaultToleranceConfigManager.putAppConfig("appName", faultToleranceConfig);
```
如上，该应用会在打开了单机故障剔除开关，每20s的时间窗口进行一次异常情况的计算，如果某个服务+ip的调用维度被判定为故障节点，则会进行将该服务+ip的权重降级为0.5倍。