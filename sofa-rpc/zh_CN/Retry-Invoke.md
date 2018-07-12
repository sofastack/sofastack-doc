## 重试调用
SOFARPC 支持进行框架层面的重试策略,前提是集群模式为 FailOver(SOFARPC 默认),只要发生服务端的框架层面异常或者是超时异常 才发起rpc异常重试,
如果是业务抛出异常,这种情况是不会重试的.

对于 SOFABoot 的应用,只需要在配置中,指定需要重试的次数.
```java
<sofa:reference jvm-first="false" id="retriesServiceReferenceBolt" interface="com.alipay.sofa.rpc.samples.retries.RetriesService">
   <sofa:binding.bolt>
     <sofa:global-attrs retries="2"/>
   </sofa:binding.bolt>
</sofa:reference>

```


而如果是直接使用 SOFARPC, 则直接设置即可

```java
ConsumerConfig<RetriesService> consumerConfig =new ConsumerConfig<RetriesService>();
consumerConfig.setRetries(2);
```

这样,在框架调用过程中,会在
```java
com.alipay.sofa.rpc.client.FailoverCluster
```
中进行读取该值,并进行相应的重试逻辑调用.


