# PromQL语法特性补充

<a name="tq3auy"></a>
## [](#tq3auy)PromQL官方文档

推荐阅读：<br />[https://prometheus.io/docs/prometheus/latest/querying/basics/](https://prometheus.io/docs/prometheus/latest/querying/basics/)

更多自定义说明：

<a name="frk7uq"></a>
## [](#frk7uq)1.  Tag选择器的“in”筛选

<a name="c379gu"></a>
### [](#c379gu)=~|
将tag符合表达式的提供的值选择出来，类似于SQL中的in语义

<a name="s5ycxb"></a>
#### [](#s5ycxb)示例

将app为 lookout-gateway 或 lookout-server的应用时序数据查询出来
```
jvm.memory.heap.used{app=~|"lookout-gateway|lookout-server",instance_id="000001"}
```

![](https://cdn.nlark.com/lark/0/2018/png/78825/1542857872892-86fb4198-24ec-402f-b050-babde14880a8.png#align=left&display=inline&height=228&originHeight=1418&originWidth=2786&status=done&width=448)


<a name="l1e1ey"></a>
## [](#l1e1ey)2. Tag选择器的"not in"筛选

<a name="kohqae"></a>
### [](#kohqae)!~|
将tag不符合表达式提供的值选择出来,类似SQL中的not in语义

<a name="axovdf"></a>
#### [](#axovdf)示例
```
jvm.memory.heap.used{app!~|"lookout-gateway|lookout-server",instance_id="000001"}
```


<a name="navglx"></a>
## [](#navglx)3. Increase2函数 （类似Increase），适用于Promethues的Counter型指标
increase 会根据根据查询步长，做个时间点上的函数估值（根据既有增长斜率）。所以可能是非整型。 如果你就想返回真实时间点的整数差值，不愿估算目标时刻的近视值。那么推荐使用 Increase2。

- 需要组合聚合函数时，记住“[Rate then sum, never sum then rate](https://www.robustperception.io/rate-then-sum-never-sum-then-rate)”（这里说的rate 与 increase 函数类似）

```
sum by (job)(Increase2(http_requests_total{job="node"}[5m]))  # This is okay
```
<a name="56fgyh"></a>
## [](#56fgyh)4. "_histogram_quantile_" 与(Lookout)自定义 "z_histogram_quantile_"

- _histogram_quantile ，是对使用prometheus client得到的 metrics buckets进行分析；_

- z_histogram_quantile，是对使用 lookout sdk的得到的 metrics buckets 进行分析;_


<a name="x9vrqm"></a>
## [](#x9vrqm)5. Promethues 一些最佳实践总结

- rate，increase 函数用于计算指标的速率，在使用时要根据指标数据的采集或上报时间间隔来进行over_time时间的控制。比如metrics数据是1分钟上报一次,如果想获取某个metric指标的1分钟的速率，应该按如下方式写promql语句


```
rate(http_requestl{token="mobile"}[2m]
```

- step 表示最终显示的**采样**（步长），如果和range （比如2m）保持一致，表示原样输出，不采样。 如果想进行采样，step > range 就行了！     所以总的来说，step >= range.


- range,  从实际使角度，推荐 range 值要**尽量明显大于**数据实际的**上报时间间隔**才有意义，比如上报单位是30秒，那range尽量>= 1分钟。


