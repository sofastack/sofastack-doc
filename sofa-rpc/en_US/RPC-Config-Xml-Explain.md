
This document describes the complete SOFARPC service publishing and reference in the SOFABoot environment.

## Publish service

```xml
 <bean id="helloSyncServiceImpl" class="com.alipay.sofa.rpc.samples.invoke.HelloSyncServiceImpl"/>
 <sofa:service ref="helloSyncServiceImpl" interface="com.alipay.sofa.rpc.samples.invoke.HelloSyncService" unique-id="">
        <sofa:binding.bolt>
            <sofa:global-attrs registry="" serialize-type="" filter="" timeout="3000" thread-pool-ref=""
                               warm-up-time="60000"
                               warm-up-weight="10" weight="100"/>
        </sofa:binding.bolt>
        <sofa:binding.rest>
        </sofa:binding.rest>
 </sofa:service>
```

| Attribute | Name | Default value | Comment |
|:--------------|:-----------------------|:-------------------|:---------------------------------------------------------------------------|
| id            | ID                     | bean名              |                                                                            |
| class | Class | None | |
| ref | Service interface implementation class | | |
| interface | Service interface (unique identifier) | | Use actual interface class for both normal calls and return calls |
| unique-id | Service tag (unique identifier) |
| filter | Filter configuration alias | | Separated by commas |
| registry | Server registry center | |  Separated by commas|
| timeout | Execution timeout period on the server | | |
| serialize-type| Serialization protocol | hessian2,protobuf | |
| timeout | Execution timeout period on the server | | |
| thread-pool-ref | Thread pool used by the current interface of the server | None |
| weight | Service static weight | | |
| warm-up-weight| Service warm-up weight | | |
| warm-up-time | Service warm-up time | | Unit: millisecond |

## Reference service

```xml
<sofa:reference jvm-first="false" id="helloSyncServiceReference"
                    interface="com.alipay.sofa.rpc.samples.invoke.HelloSyncService" unique-id="">
        <sofa:binding.bolt>
            <sofa:global-attrs type="sync" timeout="3000" callback-ref="" callback-class="" address-wait-time="1000"
                               connect.num="1" check="false" connect.timeout="1000" filter="" generic-interface=""
                               idle.timeout="1000"
                               idle.timeout.read="1000" lazy="false" loadBalancer="" registry="" retries="1"
                               serialize-type="" />
            <sofa:route target-url="xxx:12200" />
            <sofa:method name="hello" callback-class="" callback-ref="" timeout="3000" type="sync"/>
        </sofa:binding.bolt>
    </sofa:reference>
```

| Attribute | Name | Default value | Comment |
|:---|:-----|:---|-----:|
| id | ID | Generated automatically | |
| jvm-first | Whether to call the service of local machine first | true | |
| interface | Service interface (unique identifier) | | Use actual interface class for both normal calls and return calls |
| unique-id | Service tag (unique identifier) |
| type | Calling type | sync | callback,sync,future,oneway |
| filter | Filter configuration alias | | List |
| registry | Server registry center | | List |
| method | Method-level configuration | | Same as above |
| serialize-type | Serialization protocol | hessian2 | |
| target-url | Direct address | | Call service using the direct address |
| generic-interface | Generic interface | | |
Connect.timeout | Timeout period for connection establishment | 3000(cover 5000) | |
| connect.num | Connections | 1 | |
| idle.timeout | Idle timeout | | |
| idle.timeout.read | Read idle timeout | | |
| loadBalancer | Load balancing algorithm | random |
| lazy | Whether to delay establishing a persistent connection | false |
| address-wait-time | Waiting time for getting address | -1 | It depends on the implementation and may not take effect. |
| timeout | Call timeout period | 3000(cover 5000) | |
| retries | Number of retries after failure | 0 | It is related to cluster mode and is read by failover. |
Callback-class | Callback class | None | Available for callback |
Callback-ref | Callback class | None | Available for callback |