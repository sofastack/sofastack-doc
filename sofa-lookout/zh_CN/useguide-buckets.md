# 用户手册

## 分桶统计

### 场景

比如统计rpc耗时的分布，用户可以根据自己的需求设定若干个耗时区间的桶，Lookout会根据用户的设定统计落在各个桶的rpc次数并上报，这些分桶数据还可以进一步被用来计算近似的95线，99线等指标

### 使用方式：

目前DistributionSummary和Timer两个接口都提供了buckets(long[] buckets)方法，用于配置分桶
 
```Java
//将rpc耗时分成0-100ms，100-250ms，250ms-500ms，500-1000ms，1000-3000ms,3000ms以上几个区间
long[] buckets = new long[] { 100, 250, 500, 1000, 3000 }; 
Id id = registry.createId("rpc.latency").withTag("service", "orderService");
Timer timer= registry.timer(id);
timer.buckets(buckets);
timer.record(120, TimeUnit.MILLISECONDS);
...
```
### 说明
- 目前只有LookoutRegistry支持分桶统计
- 在分桶数据的基础上计算95线和99线需要服务端支持，例如prometheus提供的[histogram_quantile函数](https://prometheus.io/docs/prometheus/latest/querying/functions/#histogram_quantile())
- 上报数据：对以上实例会自动上报一个名为rpc.latency.buckets的metrics，在原有tag的基础上会增加一个tag，名称为_bucket，可能的取值为0-100,100-250,250-500,500-1000,1000-3000,3000-
- 记录分桶会增加metrics的数量，从而增加客户端的内存使用以及服务端计算存储的消耗，所以需要根据具体需求权衡使用，分桶个数不宜过多
- 需要根据具体场景设置分桶，分桶设置应尽量接近平常的数据分布，比如rpc耗时，访问缓存耗时可能需要不同的分桶

