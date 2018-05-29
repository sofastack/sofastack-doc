## 负载均衡
SOFARPC 提供多种负载均衡算法，目前支持以下五种：

| 类型              | 名称                 | 描述                                            |
|:-----------------|:--------------------|:-----------------------------------------------|
| random           | 随机算法             | 默认负载均衡算法。                                |
| localPref        | 本地优先算法          | 优先发现是否本机发布了该服务，如果没有再采用随机算法。 |
| roundRobin       | 轮询算法             | 方法级别的轮询，各个方法间各自轮询，互不影响。        |
| consistentHash   | 一致性hash算法       | 同样的方法级别的请求会路由到同样的节点。             |
| weightRoundRobin | 按权重负载均衡轮询算法 | 按照权重对节点进行轮询。性能较差，不推荐使用。        |

通过 ConsumerConfig 即可设置。
```java
ConsumerConfig consumerConfig = new ConsumerConfig();
consumerConfig.setLoadbalance("random");
```