## Load balance

SOFARPC provides a variety of load balancing algorithms and currently supports the following five types:

| Type | Name | Description |
|:-----------------|:--------------------|:-----------------------------------------------|
| random | Random algorithm | The default load balancing algorithm. |
localPref | Local preference algorithm | Firstly detect whether the service is published locally, if not, random algorithm is used. |
| roundRobin | Round Robin algorithm | Method-level polling, the polling is carried out separately to each method, without affecting each other. |
| consistentHash | Consistent hash algorithm | The same method-level request is routed to the same node. |
| weightRoundRobin | Weighted Round Robin algorithm | Poll nodes by weight. Not recommended due to poor performance. |

To use a specific load balancing algorithm, you can configure as follows:

### In XML

If you reference the service using XML, you can configure it by setting the `loadBalancer` property of the `sofa:global-attrs` tag:

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs loadBalancer="roundRobin"/>
    </sofa:binding.bolt>
</sofa:reference>
```

### In Annotation

It is currently not supported to configure a load balancing algorithm for a reference in Annotation. The function will be provided in subsequent releases.

### In API under Spring environment

If you use the API in a Spring or Spring Boot environment, you can configure it by calling the `setLoadBalancer` method of `BoltBindingParam`:

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setLoadBalancer("roundRobin");
```

### In API under non-Spring environment

If you directly use the bare API provided by SOFARPC in a non-Spring environment, you can configure it by calling the `setLoadBalancer` method of `ConsumerConfig`:

```java
ConsumerConfig consumerConfig = new ConsumerConfig();
consumerConfig.setLoadbalancer("random");
```