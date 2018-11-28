
## REST Filter

对于 REST，我们设计了一个 JAXRSProviderManager 管理器类。在服务端生效，生效时间为服务启动时。

```java
com.alipay.sofa.rpc.server.rest.RestServer#registerProvider
```

对于用户自定义的 Filter 类，可以在初始化完成后，调用

```java
com.alipay.sofa.rpc.config.JAXRSProviderManager#registerCustomProviderInstance
```
进行注册，其中自定义的 Filter 遵循 REST 的规范，需要实现如下接口：

```java
javax.ws.rs.container.ContainerResponseFilter
或者
javax.ws.rs.container.ContainerRequestFilter
```

REST server 启动之后，对于裸 SOFARPC 的使用，需要先注册，再启动服务。对于 SOFABoot 环境下的使用，也是类似的过程，具体的写法可以参考：

```java
com.alipay.sofa.rpc.server.rest.TraceRequestFilter
com.alipay.sofa.rpc.server.rest.TraceResponseFilter
```

