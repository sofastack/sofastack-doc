## 自动故障剔除

自动故障剔除会自动监控 RPC 调用的情况，对故障节点进行权重降级，并在节点恢复健康时进行权重恢复。目前支持 bolt 协议。

在 SOFABoot 中，只需要配置自动故障剔除的参数到 application.properties 即可。可以不完全配置，只配置自己关心的参数，其余参数会取默认值。需要注意的是 rpc.aft.regulation.effective 是该功能的全局开关，如果关闭则该功能不会运行，其他参数也都不生效。


| 属性 | 描述 | 默认值 |
| :--- | :--- | :--- |
| timeWindow | 时间窗口大小：对统计信息计算的周期。 | 10s |
| leastWindowCount | 时间窗口内最少调用数：只有在时间窗口内达到了该最低值的数据才会被加入到计算和调控中。 | 10次 |
| leastWindowExceptionRateMultiple | 时间窗口内异常率与服务平均异常率的降级比值：在对统计信息进行计算的时候，会计算出该服务所有有效调用ip的平均异常率，如果某个ip的异常率大于等于了这个最低比值，则会被降级。 | 6倍 |
| weightDegradeRate | 降级比率：地址在进行权重降级时的降级比率。 | 1/20 |
| weightRecoverRate | 恢复比率：地址在进行权重恢复时的恢复比率。 | 2倍 |
| degradeEffective | 降级开关：如果应用打开了这个开关，则会对符合降级的地址进行降级，否则只会进行日志打印。 | false(关闭) |
| degradeLeastWeight | 降级最小权重：地址权重被降级后的值如果小于这个最小权重，则会以该最小权重作为降级后的值。 | 1 |
| degradeMaxIpCount | 降级的最大ip数：同一个服务被降级的ip数不能超过该值。 | 2 |
| regulationEffective | 全局开关：如果应用打开了这个开关，则会开启整个单点故障自动剔除摘除功能，否则完全不进入该功能的逻辑。 | false(关闭) |



* 示例
```
com.alipay.sofa.rpc.aft.time.window=20
com.alipay.sofa.rpc.aft.least.window.count=30
com.alipay.sofa.rpc.aft.least.window.exception.rate.multiple=6
com.alipay.sofa.rpc.aft.weight.degrade.rate=0.5
com.alipay.sofa.rpc.aft.weight.recover.rate=1.2
com.alipay.sofa.rpc.aft.degrade.effective=ture
com.alipay.sofa.rpc.aft.degrade.least.weight=1
com.alipay.sofa.rpc.aft.degrade.max.ip.count=2
com.alipay.sofa.rpc.aft.regulation.effective=true
```
如上配置，打开了自动故障剔除功能和降级开关，当节点出现故障时会被进行权重降级，在恢复时会被进行权重恢复。每隔 20s 进行一次节点健康状态的度量，20s 内调用次数超过 30 次的节点才被作为计算数据，如果单个节点的异常率超过了所有节点的平均异常率的 6 倍则对该节点进行权重降级，降级的比率为 0.5 。权重最小降级到 1 。如果单个节点的异常率低于了平均异常率的 6 倍则对该节点进行权重恢复，恢复的比率为1.2 。单个服务最多降级 
 2 个ip。