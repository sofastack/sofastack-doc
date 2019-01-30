# User guide

## Bucket statistics

### Scenario

For example, to calculate the distribution of RPC time-consuming, you can set a number of time-consuming buckets according to your own requirement. Lookout will count the number of RPCs in each bucket according to your settings and report them. These buckets can be further processed to calculate approximate 95th percentile, 99th percentile and other indicators.

### How to use

Currently, both the DistributionSummary and Timer interfaces provide buckets (long[] buckets) methods for configuring buckets.
 
```Java
// Divide the time-consuming of RPC into 0-100ms, 100-250ms, 250ms-500ms, 500-1000ms, 1000-3000ms, and 3000ms above.
long[] buckets = new long[] { 100, 250, 500, 1000, 3000 }; 
Id id = registry.createId("rpc.latency").withTag("service", "orderService");
Timer timer= registry.timer(id);
timer.buckets(buckets);
timer.record(120, TimeUnit.MILLISECONDS);
...
```
### Note
- Currently, only LookoutRegistry supports bucket statistics.
- Calculating 95 line and 99 line based on bucket data requires server support, such as the [histogram_quantile function](https://prometheus.io/docs/prometheus/latest/querying/functions/#histogram_quantile) provided by prometheus .
- Report data: A metric named rpc.latency.buckets will be automatically reported for the above example. An extra tag will be added to the original tag, with the name _bucket, and the possible value ranges ​​are 0-100, 100-250, 250-500, 500- 1000, 1000-3000, and 3000+.
- Recording buckets will increase the number of metrics, thus increasing the memory usage of the client and the consumption of computing storage on the server. Therefore, the number of buckets should be according to your requirement, and should not be too many.
- You need to set the buckets according to the actual scenario. The bucket setting should be as close as possible to the normal data distribution. For example, RPC time-consuming and cache-access time-consuming use different buckets.