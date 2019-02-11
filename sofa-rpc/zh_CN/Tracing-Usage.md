默认 SOFARPC 已经集成了 SOFATracer，用户也可以使用其他的 APM 产品，如 Skywalking来实现相应的功能。详见文档：

* [SOFATracer](./SOFATracer-Usage)
* [Skywalking](./Skywalking-Usage)

如果想要关闭 SOFARPC 的链路追踪能力的话，在使用了 `rpc-sofa-boot-starter` 的情况下，可以在 `application.properties` 配置文件中配置 `com.alipay.sofa.rpc.defaultTracer=`。

在直接使用 `sofa-rpc-all` 的情况下，可以在 main 函数里面加上如下的代码来关闭 SOFARPC 的链路追踪的能力（在发布任何 SOFARPC 的服务或者引用任何 SOFARPC 的服务之前）：

```java
RpcConfigs.putValue(RpcOptions.DEFAULT_TRACER, "");
```