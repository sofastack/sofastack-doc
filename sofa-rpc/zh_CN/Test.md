# 测试

## 单元测试
单元测试例子放到自己开发的模块下。

如果依赖了第三方服务端（例如Zookeeper），请手动加入 profile。参考 `registry-zookeeper` 模块代码。

如果依赖了其它模块要集成测试，请放到 `test/test-intergrated` 模块中。

如果还依赖了第三方服务端（例如Zookeeper），请放到 `test-intergrated-3rd` 模块中。

## 性能测试
关闭了以下默认开启项目：  
`-Dcontext.attachment.enable=false -Dserialize.blacklist.enable=false -Ddefault.tracer= -Dlogger.impl=com.alipay.sofa.rpc.log.SLF4JLoggerImpl -Dmultiple.classloader.enable=false -Devent.bus.enable=false`

我们对 BOLT+hessian 进行了压测。

服务端：4C8G 虚拟机，千M网络，jdk1.8.0_111；

客户端：50个客户端并发请求。

| 协议 | 请求 | 响应 | 服务端 | TPS | 平均RT(ms) | 
| :--- | :--- | :--- | :--- | :--- | :--- | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 10000 | 1.93 |
| bolt+hessian | 1K String | 1K String | 直接返回 | 20000 | 4.13 | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 30000 | 7.32 |
| bolt+hessian | 1K String | 1K String | 直接返回 | 40000 | 15.78 | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 50000(接近极限,错误率0.3%） | 26.51 |
