
## REST filter

For REST, we designed a JAXRSProviderManager manager class. It takes effect on the server when the service starts.

```java
com.alipay.sofa.rpc.server.rest.RestServer#registerProvider
```

For the user-defined Filter class, you can call it after the initialization is complete.

```java
com.alipay.sofa.rpc.config.JAXRSProviderManager#registerCustomProviderInstance
```
To register filter, since the custom Filter follows REST specification, you need to implement the following interface:

```java
javax.ws.rs.container.ContainerResponseFilter
or
javax.ws.rs.container.ContainerRequestFilter
```

After the REST server is started, if using bare SOFARPC, you need to register filter first before starting the service. In SOFABoot environment, it is similar. The specific encoding method is as follows:

```java
com.alipay.sofa.rpc.server.rest.TraceRequestFilter
com.alipay.sofa.rpc.server.rest.TraceResponseFilter
```