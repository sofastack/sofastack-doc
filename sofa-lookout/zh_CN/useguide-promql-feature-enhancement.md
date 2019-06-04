### 1.  Tag选择器的“in”筛选


=~|
将tag符合表达式的提供的值选择出来，类似于SQL中的in语义



示例


将app为 foo 或 foo2的应用时序数据查询出来

```java
jvm.memory.heap.used{app=~|"foo|foo2",instance_id="xxx"}
```

### 2. Tag选择器的"not in"筛选


!~|
将tag不符合表达式提供的值选择出来,类似SQL中的not in语义



示例

```java
jvm.memory.heap.used{app!~|"foo|foo2",instance_id="xxx"}
```




### 3. Increase2函数 （类似Increase），适用于Promethues的Counter型指标
increase 会根据根据查询步长，做个时间点上的函数估值（根据既有增长斜率）。所以可能是非整型。 如果你就想返回真实时间点的整数差值，不愿估算目标时刻的近视值。那么推荐使用 Increase2。



需要组合聚合函数时，记住“Rate then sum, never sum then rate”（这里说的rate 与 increase 函数类似）

```java
sum by (job)(Increase2(http_requests_total{job="node"}[5m]))  # This is okay
```

### 4. "histogram_quantile" 与(Lookout)自定义 "zhistogram_quantile"
histogram_quantile ，是对使用prometheus client得到的 metrics buckets进行分析；

zhistogram_quantile，是对使用 lookout sdk的得到的 metrics buckets 进行分析;



### 5. Promethues 一些最佳实践总结
rate，increase 函数用于计算指标的速率，在使用时要根据指标数据的采集或上报时间间隔来进行over_time时间的控制。比如metrics数据是1分钟上报一次,如果想获取某个metric指标的1分钟的速率，应该按如下方式写promql语句



```java
rate(http_requestl{token="mobile"}[2m]
```


step 表示最终显示的采样（步长），如果和range （比如2m）保持一致，表示原样输出，不采样。 如果想进行采样，step > range 就行了！     所以总的来说，step >= range.



range,  从实际使角度，推荐 range 值要尽量明显大于数据实际的上报时间间隔才有意义，比如上报单位是30秒，那range尽量>= 1分钟。

