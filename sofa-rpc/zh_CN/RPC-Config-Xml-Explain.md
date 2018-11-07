
这部分介绍在 SOFABoot 环境下,完整的 SOFARPC 服务发布与引用说明

## 发布服务

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

| 属性           | 名称                    | 默认值              | 备注                                                                       |
|:--------------|:-----------------------|:-------------------|:---------------------------------------------------------------------------|
| id            | ID                     | bean名              |                                                                            |
| class         |  类                    |               无    |                                 |
| ref           | 服务接口实现类           |                    |                                                                            |
| interface     | 服务接口（唯一标识元素）  |                     | 不管是普通调用和返回调用，这里都设置实际的接口类                                 |
| unique-id     | 服务标签（唯一标识元素）  |                     |                                                                            |
| filter        | 过滤器配置别名           |                    | 多个用逗号隔开                                                               |
| registry      | 服务端注册中心           |                    |  逗号分隔                                                                       |
| timeout       | 服务端执行超时时间        |                    |                                                                            |
| serialize-type| 序列化协议              | hessian2,protobuf   |                                                                            |
| timeout       | 服务端执行超时时间        |                    |                                                                            |
| thread-pool-ref 服务端当前接口使用的线程池 | 无                 |                    |                                                                            |
| weight        | 服务静态权重             |                    |                                                                            |
| warm-up-weight| 服务预热权重             |                    |                                                                            |
| warm-up-time  | 服务预热时间             |                    |    单位毫秒                                                                        |

## 引用服务

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

| 属性 | 名称 | 默认值 | 备注 |
|:---|:-----|:---|-----:|
| id | ID | 自动生成 |  |
| jvm-first | 是否优先本地 | true |  |
| interface   | 服务接口（唯一标识元素）  |                    | 不管是普通调用和返回调用，这里都设置实际的接口类                                 |
| unique-id      | 服务标签（唯一标识元素）  |                    |                                                                            |
| type |  调用方式 |  sync |  callback,sync,future,oneway |
| filter | 过滤器配置别名 |  | List |
| registry | 服务端注册中心 |  | List |
| method | 方法级配置 |  | 说明同上 |
| serialize-type | 序列化协议 | hessian2 |  |
| target-url | 直连地址 |  | 直连后register |
| generic-interface | 泛化接口 |  |  |
| connect.timeout | 建立连接超时时间 | 3000(cover 5000) |  |
| connect.num | 连接数 | 1 |  |
| idle.timeout | 空闲超时时间 |  |  |
| idle.timeout.read | 读空闲超时时间 |  |  |
| loadBalancer | 负载均衡算法 | random |  |
| lazy | 是否延迟建立长连接 | false |  |
| address-wait-time | 等待地址获取时间 | -1 | 取决于实现，可能不生效。 |
| timeout | 调用超时时间 | 3000(cover 5000) |  |
| retries | 失败后重试次数 | 0 | 跟集群模式有关，failover读取此参数。 |
| callback-class | callback 回调类 | 无 |  callback 才可用 |
| callback-ref | callback 回调类 | 无 |  callback 才可用 |
