## Retry calling

SOFARPC supports a framework-level retry strategy when the cluster mode is FailOver (SOFARPC uses FailOver mode by default). Retry is only initiated if there is a framework-level exception or a timeout exception on the server. If the business itself throws an exception, the service will not be called again. SOFARPC does not perform any retry by default.

> Note: Although the system will retry calling in case of timeout exception, the server still needs to guarantee the idempotency of the service. Otherwise there may be risks.

### Use XML

If you subscribe to the service using XML, you can set the number of retries by setting the `retries` parameter of `sofa:global-attrs`:

```xml
<sofa:reference jvm-first="false" id="retriesServiceReferenceBolt" interface="com.alipay.sofa.rpc.samples.retries.RetriesService">
   <sofa:binding.bolt>
     <sofa:global-attrs retries="2"/>
   </sofa:binding.bolt>
</sofa:reference>
```

### Use Annotation

If you are using Annotation, you can set the `retries` attribute of `@SofaReferenceBinding` annotation:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", retries = 2))
private SampleService sampleService;
```

### Use API in Spring environment

If you are using the API in Spring environment, you can call the `setRetries` method of `BoltBindingParam`:

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setRetries(2);
```

### Use API in non-Spring environment

If you are using the bare API of SOFARPC directly in non-Spring environment, you can call the `setRetries` method of `ConsumerConfig`:

```java
ConsumerConfig<RetriesService> consumerConfig = new ConsumerConfig<RetriesService>();
consumerConfig.setRetries(2);
```