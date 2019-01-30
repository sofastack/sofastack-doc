## 重试调用

SOFARPC 支持进行框架层面的重试策略，前提是集群模式为 FailOver（SOFARPC 默认即为 FailOver 模式）。重试只有在发生服务端的框架层面异常或者是超时异常才会发起。如果是业务抛出异常，是不会重试的。默认情况下 SOFARPC 不进行任何重试。

> 请注意：超时异常虽然可以重试，但是需要服务端保证业务的幂等性，否则可能会有风险

### XML 方式

如果使用 XML 方式订阅服务，可以设置 `sofa:global-attrs` 的 `retries` 参数来设置重试次数：

```xml
<sofa:reference jvm-first="false" id="retriesServiceReferenceBolt" interface="com.alipay.sofa.rpc.samples.retries.RetriesService">
   <sofa:binding.bolt>
     <sofa:global-attrs retries="2"/>
   </sofa:binding.bolt>
</sofa:reference>
```

### Annotation 方式

如果是使用 Annotation 的方式，可以通过设置 `@SofaReferenceBinding` 注解的 `retries` 属性来设置：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", retries = 2))
private SampleService sampleService;
```

### Spring 环境下 API 方式

如果是在 Spring 环境下用 API 的方式，可以调用 `BoltBindingParam` 的 `setRetries` 方法来设置：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setRetries(2);
```

### 非 Spring 环境下 API 方式

如果是在非 Spring 环境下直接使用 SOFARPC 的裸 API 的方式，可以通过调用 `ConsumerConfig` 的 `setRetries` 方法来设置：

```java
ConsumerConfig<RetriesService> consumerConfig = new ConsumerConfig<RetriesService>();
consumerConfig.setRetries(2);
```