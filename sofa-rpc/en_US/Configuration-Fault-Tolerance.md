## Fault tolerance

Fault tolerance automatically monitors the RPC calls, degrades the weight of the failed node, and recovers the weight when the node restored to normal. The bolt protocol is currently supported.

In SOFABoot, you only need to configure fault tolerance parameters to `application.properties`. You can select not to configure all parameters but only configure the parameters that you care about. Then, the remaining parameters will take the default values. Note that `rpc.aft.regulation.effective` is a global switch for this function. If it is off, the function will not work and other parameters will not take effect.


| Attribute | Description | Default value |
| :--- | :--- | :--- |
| timeWindow | Time window size: the period in which statistics are calculated. | 10s |
| leastWindowCount | Minimum number of calls in the time window: Only data that has reached this minimum value in the time window will be added in calculation and control. | 10 times |
| leastWindowExceptionRateMultiple | Degradation ratio of the exception rate in the time window to the average exception rate of the service: When calculating the statistical information, the average exception rate of all valid call IPs of the service is calculated. If the exception rate of an IP is greater than or equal to the lowest ratio, the IP will be degraded. | 6 times |
| weightDegradeRate | Degradation ratio: The rate of degradation of an address when it is degraded. | 1/20 |
| weightRecoverRate | Recovery ratio: The recovery ratio of the address when it is weighted. | 2 times |
| degradeEffective | Degradation switch: If the application turns on this switch, it will degrade the address that matches the degradation criteria; otherwise, only the log will be printed. | false (off) |
| degradeLeastWeight | Degradation minimum weight: If the address weight is degraded to the weight less than this minimum weight, the minimum weight will be used. | 1 |
| degradeMaxIpCount | Maximum number of IPs for degradation: The number of IPs in the same service that have been degraded cannot exceed this value. | 2 |
| regulationEffective | Global switch: If the switch is turned on by the application, the entire standalone fault tolerance function will be turned on; otherwise, this function will not be used at all. | false (off) |


* Example
```
Com.alipay.sofa.rpc.aft.time.window=20
Com.alipay.sofa.rpc.aft.least.window.count=30
Com.alipay.sofa.rpc.aft.least.window.exception.rate.multiple=6
Com.alipay.sofa.rpc.aft.weight.degrade.rate=0.5
Com.alipay.sofa.rpc.aft.weight.recover.rate=1.2
Com.alipay.sofa.rpc.aft.degrade.effective=ture
Com.alipay.sofa.rpc.aft.degrade.least.weight=1
Com.alipay.sofa.rpc.aft.degrade.max.ip.count=2
Com.alipay.sofa.rpc.aft.regulation.effective=true
```
As configured above, the fault tolerance function and degradation switch are enabled. When a node fails too many times, its weight is degraded, and during recovery, the weight will be restored. The node healthy is measured every 20s, and the nodes called for more than 30 times in 20s are recognized as calculation data. If the exception rate of a single node exceeds 6 times of the average exception rate of all nodes, the node will be degraded, and the degradation ratio is 0.5. The weight can be degraded to a minimum of 1. If the exception rate of a single node is less than 6 times of the average exception rate, the node weight will be restored, and the recovery ratio is 1.2. In a single service, 2 IPs can be degraded at most.