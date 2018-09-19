# 用户手册

## 客户端 API 使用说明

SOFALookout 客户端设计上保持了 API 与实现解耦。如果我们只需要基于 SOFALookout API 进行埋点，那么只需要依赖 API 包即可。在没有依赖具体实现模块依赖时（比如 client 依赖 或 SOFABoot（Spring Boot）Start 依赖），API 包会自动使用 NoopRegistry，使得所有埋点的地方都已空实现替代。

### 1.API 依赖引入

```xml
<dependency>
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-api</artifactId>
    <version>${lookout.client.version}</version>
</dependency>
```

### 2.关于 ID

Lookout metrics 相比传统的 metrics 库单一维度的信息描述，提供了支持多维度描述的 tags 能力。 Lookout metrics 的唯一标识 Id 类，由 name 和 tags 构成。

```java
Id basicId = registry.createId("rpc.provider.service.stats");
id = basicId.withTag("service", "com.alipay.demo.demoService")
            .withTag("method", "sayHi")
            .withTag("protocol", "tr")
            .withTag("alias", "group1");
```

上面是 Id 的简单示例了，如何创建 Id，如何打 tag，（每打一次 tag，都会生成并返回一个新的 Id 对象引用）切记！使用返回新的 Id 对象了哦。

* 不要主动缓存 Id 或具体的 Metric 对象,Lookout 的 Registry 已经登记记录了。相等的 Id （ name 和 tags 一样）会复用已有的Id 对应 Metric 对象。

#### 2.1 Priority tag (不是必须)

PRIORITY 枚举级别: HIGH, NORMAL, LOW;

```java
id.withTag(LookoutConstants.LOW_PRIORITY_TAG);
```
如果不打该 Tag (建议)，默认是 NORMAL 级别。级别代表了采集间隔周期（HIGH：2s, NORMAL: 30s, LOW: 1min）

#### 2.2 关于 tags

- 通用的 tags，比如：本机 ip，机房等详细会统一附上，不需要单独指定。
- 普通 Java 项目直接 lookout-client 时，tags 需要自己指定，特别记住：`appName 别忘啦！tag:app=xx`
- key 必须小写，尽量是字母，数字，下划线；
（尤其是运行期的 Counter, Timer, DistributeSummary 这些 metrics）某个类型的 tag 的 values 尽量是稳定不变有限集合，而且 tags 尽量少，防止 metrics 数量超过最大限制！ 比如：rpc 场合 service, method 两个 tag 对应的值是有限较小的；
反例是每次 rpc 调用都有是个独立的 tag-value。 因此，总体原则自定义打 tags 时要尽量少，对应 values 的集合数量尽量小；
专门用途的 TAG 名称: "priority": 表示优先级。
- 系统保留的 tag key 统配格式`_*_`,以下划线开始，以下划线结束（比如: "_type_" ）。 `请不要使用这种格式的 key，可能会被系统覆盖或丢弃`

### 3.可接入的统计( Metric )类型API

#### Counter 「计数器」

- 场景：方法调用次数；
- 主动汇报的数据包括： count, rate (也就是 qps)；
- 使用方式

```java
Counter counter=registry.counter(id);
counter.inc();
```

#### Timer 「耗时统计器」
- 场景:统计任务，方法耗时，支持[分桶统计](./useguide-buckets)
- 主动汇报的数据包括：elapPerExec (单次执行耗时), total 耗时，Max 耗时,（上报单位：秒）；
- 使用方式

```java
Timer timer=registry.timer(id);
timer.record(2, TimeUnit.SECONDS);
```
#### DistributionSummary 「值分布情况统计器」

- 场景：比如 io 流量，支持[分桶统计](./useguide-buckets)
- 主动汇报的数据包括: count, total(size), max(size)；
- 使用方式:

```java
DistributionSummary distributionSummary=registry.distributionSummary(id);
distributionSummary.record(1024);
```
#### Gauge 「即时数据观察」

- 场景：比如线程池，内存值等即时值；
- 主动汇报的数据包括: value；
往注册表中登记新 gauge 时，ID 值相等，注册表继续使用已有的(忽略新的)；

*注意，推荐 gauge 观察的对象尽量是单例的（复用），并且建议在运行时一直活着（而不是作为一个临时的统计）！，如果不是单例或者只存活一段时间，那么一定要从 Registry 中 remove 掉，否则影响 GC（主要是它持有的外部对象引用无法释放，浪费空间）!!*

- 使用方式:

```Java
registry.gauge(id,new Gauge<Double>() {
    @Override
    public Double value() {
        return 0.1;
    }
});
```
#### MixinMetric 「上述基本统计 metrics 的混合管理体」

MixinMetric 是 Lookout 特有的，表示多个基本 metrics 的混合体。引入该 Mixin 目的是优化对「同一度量目标」（即测量目标一致，tags 一致）的多测量指标传输和存储效率，比如：同一线程池的各种指标(线程总数，活跃总数，等待队列大小...)。

- 使用方式（比如，对一次服务调用，加入多个测量指标：调用耗时，输入字节，调用次数，输出字节等等）

```Java
  //1. getOrAdd  MixinMetric
  MixinMetric rpcServiceMetric=registry.minxinMetric(id);

  //2. getOrAdd basic component metric to use
  Timer rpcTimer = rpcServiceMetric.timer("perf");
  DistributionSummary rpcOutSizeMetric = rpcServiceMetric.distributionSummary("inputSize");
```
#### Top 工具

- 场景：列出 TOP 5/10/20...（前 5/10/20... 名）度量信息；

- 具体背景: 当希望更细粒度地跟踪和度量时，比如某条 sql 语句（某个 URL ）的请求耗时，你可能需要将 sql (或 url )作为一个tag ,比如:

```Java
sql_execute_cost:sql=select..
sql_execute_cost:sql=insert..
sql_execute_cost:sql=insert2..
...
```

这样产生的 metrics 就会非常多!而 metrics 总数量(避免影响业务)上限一般控制在 5k 左右，所以建议尽量少的 tags,每个 tag values 尽量可枚举，且数量也尽量少。

但如果你确实希望定位到一些细粒度的问题，那么推荐使用 TopUtil（它可以只记录前几名的数据，淘汰掉其他数据）。

- 使用方式

```Java
TopGauger topGauger = TopUtil.topGauger(registry, registry.createId("top5sql"), 5);

topGauger.record(1000l, new BasicTag("sql1", "select1"));
topGauger.record(2000l, new BasicTag("sql2", "select2"));
...
```


