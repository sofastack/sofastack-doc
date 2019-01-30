# User guide

## Use client API

In the design of SOFALookout client, API is decoupled from the implementation. If you need to log the events based on the SOFALookout API, you only need to add the `lookout-api` Maven dependency to the `pom.xml` file in your application/project. If the dependencies (such as client dependencies or SOFABoot (Spring Boot) Starter) do not exist, the API package uses NoopRegistry automatically, to replace all the locations of which the events are logged.

### 1.Introduce API dependency

```xml
<dependency>
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-api</artifactId>
    <version>${lookout.client.version}</version>
</dependency>
```

### 2.About ID

Compared to the traditional metrics library's single-dimensional information description, Lookout metrics provides tag capability that supports multi-dimensional descriptions. ID class, the unique identification of Lookout metrics, consists of name and tags.

```java
Id basicId = registry.createId("rpc.provider.service.stats");
id = basicId.withTag("service", "com.alipay.demo.demoService")
            .withTag("method", "sayHi")
            .withTag("protocol", "tr")
            .withTag("alias", "group1");
```

The above is a simple example of ID introducing how to create ID and how to tag. Note that every time you tag, a new ID object is generated and returned.

> Do not proactively cache Id or the specific Metric object, since Lookout's Registry has already recorded. When using a same Id (with the same name and tags), the existing Id and its corresponding Metric object will be reused.

#### 2.1 Priority tag (optional)

PRIORITY enumeration level: HIGH, NORMAL, LOW.

```java
id.withTag(LookoutConstants.LOW_PRIORITY_TAG);
```
It is recommended that you do not add this tag, the default level will be NORMAL. The level represents the collection interval (HIGH: 2s, NORMAL: 30s, LOW: 1min).

#### 2.2 About tags

- General tags, such as local IP, data center, and other details, will be attached and no need to be specified separately.
- In a non-SOFABoot project, you must manually add tags to the client, especially the `app` tag which specifies the app name:  `app=appName`.
- key contains only lowercase letters, numbers, and underscores.
(especially the metrics at runtime, such as Counter, Timer, and DistributeSummary) The values ​​of a tag shall be within a stable finite set. Try to use as few tags as possible to prevent the number of metrics from exceeding the maximum limit. For example: In RPC service, the value of method's two tags shall be as few as possible.
The counterexample is that each RPC call has a separate tag-value. Therefore, the overall principle is that there should be as few custom tags as possible, and the number of sets of the values ​​should be as small as possible.
Specialized TAG name "priority" indicates priority.
- The tag key reserved by the system is `_*_`. Starting with an underscore and ending with an underscore (eg: "_type_" ). Do not use this format for keys, since it may be overwritten or discarded by the system.

### 3. Accessible Statistics (Metric) Type API

#### Counter
- Scenario: Number of method calls;
- Actively reported data includes: count, rate (namely qps);
- How to use:

```java
Counter counter=registry.counter(id);
counter.inc();
```

#### Timer
- Scenario: Statistical tasks, method time-consuming, support [bucket statistics](./useguide-buckets)
- Actively reported data includes: elapPerExec (single execution time), total time-consuming, Max time-consuming, in seconds;
- How to use:

```java
Timer timer=registry.timer(id);
timer.record(2, TimeUnit.SECONDS);
```

#### DistributionSummary
- Scenario: such as IO traffic, support [bucket statistics](./useguide-buckets)
- Actively reported data includes: count, total(size), max(size);
- How to use:

```java
DistributionSummary distributionSummary=registry.distributionSummary(id);
distributionSummary.record(1024);
```

#### Gauge (real-time data observation)
- Scenario: Real-time values ​​such as thread pools, memory values.
- Actively reported data includes: value;
When the new gauge is registered in the registry, if the ID values ​​are the same, the registry continues to use the existing ones (ignoring the new ones);

> Note: It is recommended that the object observed by gauge is singleton (reuse), and it is always alive during runtime (rather than as a temporary statistic). If it is not singleton or only be alive for some time, then it must be removed from the Registry, otherwise it will affect the GC, and the external object reference it holds cannot be released.

- How to use:

```Java
registry.gauge(id,new Gauge<Double>() {
    @Override
    public Double value() {
        return 0.1;
    }
});
```

#### MixinMetric (The hybrid manager of the above basic statistics metrics)

MixinMetric is unique to Lookout and represents a mixture of multiple basic metrics. The purpose of introducing this Mixin is to optimize the transmission and storage efficiency of multiple metrics indicators for the same metric target (that is, the measurement target is consistent and the tags are consistent). For example: Various indicators of the same thread pool (total number of threads, total active count, waiting queue size).

- How to use: For example, for a service call, add multiple measurement indicators: call time-consuming, input byte count, number of calls, output byte count, etc.

```Java
  //1. getOrAdd MixinMetric
  MixinMetric rpcServiceMetric=registry.minxinMetric(id);

  //2. getOrAdd basic component metric to use
  Timer rpcTimer = rpcServiceMetric.timer("perf");
  DistributionSummary rpcOutSizeMetric = rpcServiceMetric.distributionSummary("inputSize");
```

#### Top Tools

- Scenario: List the metrics information of TOP 5/10/20;
- Background: When you want to track and measure more finely, such as the request time-consuming of a SQL statement (an URL), you may need to use SQL (or URL) as a tag, such as:

```Java
Sql_execute_cost:sql=select..
Sql_execute_cost:sql=insert..
Sql_execute_cost: sql=insert2..
...
```

The resulting metrics will be too much. The upper limit of the total number of metrics (to avoid affecting the business) is generally at around 5000. So it is recommended to have as few tags as possible, and each tag values ​​should be enumerated as much as possible, and the number should be as small as possible.

But if you really want to target some fine-grained problems, then it is recommended to use TopUtil (it can only record the first few data and eliminate other data).

- How to use:

```Java
TopGauger topGauger = TopUtil.topGauger(registry, registry.createId("top5sql"), 5);

topGauger.record(1000l, new BasicTag("sql1", "select1"));
topGauger.record(2000l, new BasicTag("sql2", "select2"));
...
```