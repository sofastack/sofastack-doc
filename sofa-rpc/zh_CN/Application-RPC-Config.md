在 SOFABoot 的使用场景下，RPC 框架在应用层面，提供一些配置参数，支持的应用级别的参数配置，如端口，线程池等信息，都是通过
Spring Boot的`@ConfigurationProperties` 进行的绑定。绑定属性类是`com.alipay.sofa.rpc.boot.config.SofaBootRpcProperties`，配置前缀是

```java

static final String PREFIX = "com.alipay.sofa.rpc";
``` 

那么在 application.properties 文件中，目前可以配置以下几个选项。其中使用者也可以根据自己的编码习惯，按照 Spring Boot的规范，按照驼峰，中划线等进行书写。

```xml
# 单机故障剔除
com.alipay.sofa.rpc.aft.regulation.effective # 是否开启单机故障剔除功能
com.alipay.sofa.rpc.aft.degrade.effective  # 是否开启降级
com.alipay.sofa.rpc.aft.time.window # 时间窗口
com.alipay.sofa.rpc.aft.least.window.count # 最小调用次数
com.alipay.sofa.rpc.aft.least.window.exception.rate.multiple # 最小异常率
com.alipay.sofa.rpc.aft.weight.degrade.rate # 降级速率
com.alipay.sofa.rpc.aft.weight.recover.rate # 恢复速率
com.alipay.sofa.rpc.aft.degrade.least.weight #降级最小权重
com.alipay.sofa.rpc.aft.degrade.max.ip.count # 最大降级 ip

# bolt
com.alipay.sofa.rpc.bolt.port # bolt 端口
com.alipay.sofa.rpc.bolt.thread.pool.core.size # bolt 核心线程数
com.alipay.sofa.rpc.bolt.thread.pool.max.size # bolt 最大线程数
com.alipay.sofa.rpc.bolt.thread.pool.queue.size # bolt 线程池队列
com.alipay.sofa.rpc.bolt.accepts.size # 服务端允许客户端建立的连接数

# rest
com.alipay.sofa.rpc.rest.hostname # rest hostname
com.alipay.sofa.rpc.rest.port # rest port
com.alipay.sofa.rpc.rest.io.thread.size # rest io 线程数
com.alipay.sofa.rpc.rest.context.path # rest context path
com.alipay.sofa.rpc.rest.thread.pool.core.size # rest 核心线程数
com.alipay.sofa.rpc.rest.thread.pool.max.size # rest 最大线程数
com.alipay.sofa.rpc.rest.max.request.size # rest 最大请求大小
com.alipay.sofa.rpc.rest.telnet # 是否允许 rest telnet
com.alipay.sofa.rpc.rest.daemon # 是否hold住端口，true的话随主线程退出而退出

# dubbo
com.alipay.sofa.rpc.dubbo.port # dubbo port
com.alipay.sofa.rpc.dubbo.io.thread.size # dubbo io 线程大小
com.alipay.sofa.rpc.dubbo.thread.pool.max.size # dubbo 业务线程最大数
com.alipay.sofa.rpc.dubbo.accepts.size # dubbo 服务端允许客户端建立的连接数
com.alipay.sofa.rpc.dubbo.thread.pool.core.size #dubbo 核心线程数
com.alipay.sofa.rpc.dubbo.thread.pool.queue.size #dubbo 最大线程数

# registry
com.alipay.sofa.rpc.registry.address # 注册中心地址
com.alipay.sofa.rpc.virtual.host # virtual host
com.alipay.sofa.rpc.bound.host # 绑定 host
com.alipay.sofa.rpc.virtual.port # virtual端口
com.alipay.sofa.rpc.enabled.ip.range # 多网卡 ip 范围
com.alipay.sofa.rpc.bind.network.interface # 绑定网卡

# h2c
com.alipay.sofa.rpc.h2c.port # h2c 端口
com.alipay.sofa.rpc.h2c.thread.pool.core.size # h2c 核心线程数
com.alipay.sofa.rpc.h2c.thread.pool.max.size # h2c 最大线程数
com.alipay.sofa.rpc.h2c.thread.pool.queue.size # h2c 队列大小
com.alipay.sofa.rpc.h2c.accepts.size # 服务端允许客户端建立的连接数

# 扩展
com.alipay.sofa.rpc.lookout.collect.disable # 是否关闭 lookout

# 代理
com.alipay.sofa.rpc.consumer.repeated.reference.limit # 允许客户端对同一个服务生成的引用代理数量，默认为3;
```
