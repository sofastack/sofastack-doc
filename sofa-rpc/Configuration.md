
## RegistryConfig
| 属性            | 名称                        | 默认值     | 备注                    |
|:---------------|:---------------------------|:----------|:-----------------------|
| protocol       | 协议                        | zookeeper | 目前支持zookeeper和local |
| address        | 注册中心地址                 |           | 和index属性必选一个      |
| index          | 指定注册中心寻址服务的地址     |           | 和address属性必选一个    |
| register       | 是否注册服务                 | true      |                        |
| subscribe      | 是否订阅服务                 | true      |                        |
| timeout        | 调用注册中心超时时间          | 10s       |                        |
| connectTimeout | 连接注册中心超时时间          | 20s       |                        |
| file           | local协议时使用的本地文件位置 | $HOME     |                        |

## ServerConfig
| id             | Id                    | 默认值                 | 备注                                                 |
|:---------------|:----------------------|:-----------------------|:----------------------------------------------------|
| protocol       | 协议                   | bolt                   | 目前支持bolt，rest，dubbo                             |
| host           | 主机                   | 0.0.0.0                |                                                     |
| port           | 端口                   | 12200                  |                                                     |
| contextPath    | 上下文路径             | /                      |                                                     |
| ioThreads      | IO线程池数             | 0                      | 取决于实现，可能不生效。例如bolt默认cpu*2。0表示自动计算。 |
| threadPoolType | 业务线程池类型          | cached                 |                                                     |
| coreThreads    | 业务线程池核心大小       | 80(override 20)        |                                                     |
| maxThreads     | 业务线程池最大值        | 400(override 200)      |                                                     |
| telnet         | 是否允许telnet         | true                   | 取决于实现，可能不生效。例如bolt不支持telnet。            |
| queueType      | 业务线程池类型          | normal                 | 另外还有优先级队列等                                   |
| queues         | 业务线程池队列          | 1000(override 0)       |                                                     |
| aliveTime      | 业务线程池存活时间       | 300000(override 60000) |                                                     |
| preStartCore   | Id                    | 自动生成                 |                                                     |
| accepts        | 最大长连接数量          | 100000                 | 取决于实现，可能不生效。                                |
| serialization  | 序列化协议             | hesisan2               |                                                     |
| virtualHost    | 虚拟主机地址            |                        | 注册到注册中心时优先使用                                |
| virtualPort    | 虚拟主机端口            |                        | 注册到注册中心时优先使用                                |
| epoll          | 使用                   | false                  | 取决于实现，可能不生效。                                |
| daemon         | 是否守护端口            | true                   | true的话随主线程退出而退出，false的话则要主动退出         |
| adaptivePort   | 是否调整端口            | false                  | 当端口被占用，自动+1进行适应                            |
| transport      | 传输层实现             | bolt (cover netty4)    | 取决于实现，可能不生效                                  |
| autoStart      | 是否自动启动端口        | true                   |                                                     |
| stopTimeout    | 优雅关闭超时时间（毫秒） | 10000(override 20000)  |                                                     |
| boundHost      | 绑定的地址             |                        | 默认取host值。                                        |
| parameters     | 额外属性               |                        | Map<String, String>                                 |

## ProviderConfig
| 属性           | 名称                    | 默认值              | 备注                                                                       |
|:--------------|:-----------------------|:-------------------|:---------------------------------------------------------------------------|
| id            | ID                     | 自动生成            |                                                                            |
| application   | 应用对象                | 空ApplicationConfig |                                                                            |
| interfaceId   | 服务接口（唯一标识元素）  |                    | 不管是普通调用和返回调用，这里都设置实际的接口类                                 |
| uniqueId      | 服务标签（唯一标识元素）  |                    |                                                                            |
| version       | 服务版本（唯一标识元素）  |                    |                                                                            |
| filterRef     | 过滤器配置实例           |                    | List                                                                       |
| filter        | 过滤器配置别名           |                    | 多个用逗号隔开                                                               |
| registry      | 服务端注册中心           |                    | List                                                                       |
| methods       | 方法级配置              |                    | Map<String, MethodConfig>                                                  |
| serialization | 序列化协议              | hessian2           |                                                                            |
| register      | 是否注册                | true               | 取决于实现，可能不生效。                                                      |
| subscribe     | 是否订阅                | true               | 取决于实现，可能不生效。                                                      |
| proxy         | 代理类型                | javassist          | 还有JDK动态代理                                                             |
| group         | 服务分组                |                    |                                                                            |
| ref           | 服务接口实现类           |                    |                                                                            |
| server        | 服务端                  |                    | List，可以一次发到多个服务端                                                  |
| delay         | 服务延迟发布时间         |                    | 服务延迟                                                                    |
| weight        | 服务静态权重             |                    |                                                                            |
| include       | 包含的方法              |                    |                                                                            |
| exclude       | 不包含的方法             |                    |                                                                            |
| dynamic       | 是否动态注册             |                    |                                                                            |
| priority      | 服务优先级              |                    |                                                                            |
| bootstrap     | 服务发布启动器           | bolt               |                                                                            |
| executor      | 自定义线程池             |                    |                                                                            |
| timeout       | 服务端执行超时时间        |                    |                                                                            |
| concurrents   | 并发执行请求             |                    | 接口下每方法的最大可并行执行请求数，配置-1关闭并发过滤器，等于0表示开启过滤但是不限制 |
| cacheRef      | 结果缓存实现类           |                    |                                                                            |
| mocakRef      | Mock实现类              |                    |                                                                            |
| mock          | 是否开启Mock            |                    |                                                                            |
| validation    | 是否开启参数验证(jsr303) |                    |                                                                            |
| compress      | 是否启动压缩             | false              |                                                                            |
| cache         | 是否启用结果缓存         | false              |                                                                            |
| parameters    | 额外属性                |                    | Map<String, String>                                                        |
  
## ConsumerConfig
| 属性 | 名称 | 默认值 | 备注 |
|:---|:-----|:---|-----:|
| id | ID | 自动生成 |  |
| application | 应用对象 | 空ApplicationConfig |  |
| interfaceId | 服务接口（唯一标识元素） |  | 不管是普通调用和返回调用，这里都设置实际的接口类 |
| uniqueId | 服务标签（唯一标识元素） |  |  |
| version | 服务版本（唯一标识元素） |  |  |
| filterRef | 过滤器配置实例 |  | List |
| filter | 过滤器配置别名 |  | List |
| registry | 服务端注册中心 |  | List |
| methods | 方法级配置 |  | Map<String, MethodConfig> |
| serialization | 序列化协议 | hessian2 |  |
| register | 是否注册 | true | 取决于实现，可能不生效。 |
| subscribe | 是否订阅 | true | 取决于实现，可能不生效。 |
| proxy | 代理类型 | javassist | 还有JDK动态代理 |
| group | 服务分组 |  |  |
| protocol | 调用的协议 | bolt | 目前支持bolt，rest，dubbo |
| directUrl | 直连地址 |  | 直连后register |
| generic | 是否泛化调用 | false |  |
| connectTimeout | 建立连接超时时间 | 3000(cover 5000) |  |
| disconnectTimeout | 断开连接等等超时时间 | 5000(cover 10000) |  |
| cluster | 集群模式 | failover |  |
| connectionHolder | 连接管理器实现 | all |  |
| loadBalancer | 负载均衡算法 | random |  |
| lazy | 是否延迟建立长连接 | false |  |
| sticky | 是否使用粘性连接 | false | 跳过负载均衡算法使用上一个地址 |
| inJVM | 是否转为JVM调用 | true | JVM发现服务提供者，转为走本地 |
| check | 是否检查强依赖 | false | 无可用服务端启动失败 |
| heartbeat | 心跳间隔 | 30000 | 客户端给服务端发心跳间隔。取决于实现，可能不生效。 |
| reconnect | 重连间隔 | 10000 | 客户端重建端口长连接的间隔。取决于实现，可能不生效。 |
| router | 路由器配置别名 | | List |
| routerRef | 路由器配置实例 | | List |
| bootstrap | 服务引用启动器 | bolt |  |
| addressWait | 等待地址获取时间 | -1 | 取决于实现，可能不生效。 |
| timeout | 调用超时时间 | 3000(cover 5000) |  |
| retries | 失败后重试次数 | 0 | 跟集群模式有关，failover读取此参数。 |
| invokeType | 调用类型 | sync |  |
| onReturn | 并发执行请求数 | | 接口下每方法的最大可并行执行请求数，<br>配置-1关闭并发过滤器，等于0表示开启过滤但是不限制 |
| cacheRef | 结果缓存实现类 | |  |
| mocakRef | Mock实现类 | |  |
| cache | 是否启用结果缓存 | false |  |
| mock | 是否开启Mock | |  |
| validation | 是否开启参数验证 | | 基于JSR303 |
| compress | 是否启动压缩 | false |  |
| parameters | 额外属性 | | Map<String, String> |


## MethodConfig
| 属性 | 名称 | 默认值 | 备注 |
| :--- | :--- | :--- | :---|
| name | 方法名 |  |  |
| timeout | 调用超时时间 | null |  |
| retries | 失败后重试次数 | null |  |
| invokeType | 调用类型 | null |  |
| validation | 是否开启参数验证 | null | 基于JSR303 |
| onReturn | 返回时调用的SofaResponseCallback | null | 用于实现Callback等 |
| concurrent | 并发执行请求数 | null | 接口下每方法的最大可并行执行请求数，配置-1关闭并发过滤器，等于0表示开启过滤但是不限制。 |
| validation | 是否开启参数验证 | null |  |
| compress | 是否启动压缩 | null |  |
| parameters | 额外属性 |  | Map<String, String> |


## 预热转发
| 属性 | 描述 | 默认值 | 备注 |
| :--- | :--- | :--- | :--- |
| during | 预热时间 | 0 |  |
| weightStarting | 预热期转发比例 | 1 | RPC框架内部会在集群中随机 找一台机器以此权重转出 |
| weightStarted | 预热完成后转发比例 | 0 | 预热结束后将会一直生效 |
| address | 预热期过后的转发地址 | 空 | 预热后将会一直生效 |
| uniqueId | 同 appName 多集群部署的情况下，要区别不同集群可以通过配置此项区分。指定一个自定义的系统变量（例如机房、分组等），保证集群唯一即可。 |  | 注意该uniqueId并不是配置字符串中的uniqueid。在纯RPC环境中，需要手动设置到TransmitConfig中；在SOFABoot 环境中，配置字符串中的uniqueId作为key，SOFA Boot 会通过该key动态从配置文件中读取value作为uniqueid值设置到RPC中。 |


## 自动故障剔除
| 属性 | 描述 | 默认值 |
| :--- | :--- | :--- |
| timeWindow | 时间窗口大小：对统计信息计算的周期。 | 10s |
| leastWindowCount | 时间窗口内最少调用数：只有在时间窗口内达到了该最低值的数据才会被加入到计算和调控中。 | 10次 |
| leastWindowExceptionRateMultiple | 时间窗口内异常率与服务平均异常率的降级比值：在对统计信息进行计算的时候，会计算出该服务所有有效调用ip的平均异常率，如果某个ip的异常率大于等于了这个最低比值，则会被降级。 | 6倍 |
| weightDegradeRate | 降级比率：地址在进行权重降级时的降级比率。 | 1/20 |
| weightRecoverRate | 恢复比率：地址在进行权重恢复时的恢复比率。 | 2倍 |
| degradeEffective | 降级开关：如果应用打开了这个开关，则会对符合降级的地址进行降级，否则只会进行日志打印。 | false(关闭) |
| degradeLeastWeight | 降级最小权重：地址权重被降级后的值如果小于这个最小权重，则会以该最小权重作为降级后的值。 | 0 |
| degradeMaxIpCount | 降级的最大ip数：同一个服务被降级的ip数不能超过该值。 | 2 |
| regulationEffective | 全局开关：如果应用打开了这个开关，则会开启整个单点故障自动剔除摘除功能，否则完全不进入该功能的逻辑。 | false(关闭) |