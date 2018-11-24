## 负载均衡

SOFARPC 提供多种负载均衡算法，目前支持以下五种：

| 类型              | 名称                 | 描述                                            |
|:-----------------|:--------------------|:-----------------------------------------------|
| random           | 随机算法             | 默认负载均衡算法。                                |
| localPref        | 本地优先算法          | 优先发现是否本机发布了该服务，如果没有再采用随机算法。 |
| roundRobin       | 轮询算法             | 方法级别的轮询，各个方法间各自轮询，互不影响。        |
| consistentHash   | 一致性hash算法       | 同样的方法级别的请求会路由到同样的节点。             |
| weightRoundRobin | 按权重负载均衡轮询算法 | 按照权重对节点进行轮询。性能较差，不推荐使用。        |

要使用某种特定的负载均衡算法，可以按照以下的方式进行设置：

### XML 方式

如果使用 XML 的方式引用服务，可以通过设置 `sofa:global-attrs` 标签的 `loadBalancer` 属性来设置：

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs loadBalancer="roundRobin"/>
    </sofa:binding.bolt>
</sofa:reference>
```

### Annotation 方式

Annotation 方式目前暂未提供设置某一个 Reference 的负载均衡算法的方式。将在后续的版本中提供。

### 在 Spring 环境下 API 方式

如果在 Spring 或者 Spring Boot 的环境下使用 API，可以通过调用 `BoltBindingParam` 的 `setLoadBalancer` 方法来设置：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setLoadBalancer("roundRobin");
```

### 非 Spring 环境下 API 方式

如果在非 Spring 环境下直接使用 SOFARPC 提供的裸 API，可以通过调用 `ConsumerConfig` 的  `setLoadBalancer` 方法来设置：

```java
ConsumerConfig consumerConfig = new ConsumerConfig();
consumerConfig.setLoadbalancer("random");
```