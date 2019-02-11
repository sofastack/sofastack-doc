By default, SOFARPC has integrated SOFATracer. Also, you can use other APM products, such as Skywalking, to achieve the corresponding functions. For details, see the relevant documents:

* [SOFATracer](./SOFATracer-Usage)
* [Skywalking](./Skywalking-Usage)

If you want to disable the tracing ability of SOFARPC, you can do it in two ways.

If you are using `rpc-sofa-boot-starter` in SOFABoot or Spring Boot environment, you can add a configuration `com.alipay.sofa.rpc.defaultTracer=` in `application.properties`.

If you are using `sofa-rpc-all` directly, you can add the following code in the main method of your application before publish any SOFARPC service or create any SOFARPC reference.

```java
RpcConfigs.putValue(RpcOptions.DEFAULT_TRACER, "");
```