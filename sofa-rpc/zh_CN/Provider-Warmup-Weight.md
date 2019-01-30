## 预热权重

预热权重功能让客户端机器能够根据服务端的相应权重进行流量的分发。该功能也常被用于集群内少数机器的启动场景。利用流量权重功能在短时间内对服务端机器进行预热，然后再接收正常的流量比重。
运行机制如下：
![预热权重示意图](./resources/ug_1.png)

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
```xml
<sofa:reference id="sampleRestFacadeReferenceBolt" interface="com.alipay.sofa.endpoint.facade.SampleFacade">
    <sofa:binding.bolt>
         <sofa:global-attrs weight="100" warm-up-time="10000" warm-up-weight="1000"/>
     </sofa:binding.bolt>
</sofa:reference>
```