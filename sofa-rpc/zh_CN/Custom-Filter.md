## 自定义 Filter

SOFA-RPC 提供了一套良好的可扩展性机制，为各个模块提供 SPI 的能力。 SOFA-RPC 对请求与响应的过滤链处理方式是通过多个过滤器 Filter 来进行具体的拦截处理，该部分可由用户自定义 Filter 扩展，自定义 Filter 的执行顺序在内置 Filter 之后。具体方式如下：

### Bolt Filter

1. 新建自定义 Filter 。
```java
public class CustomFilter extends Filter {    
    @Override    
    public boolean needToLoad(FilterInvoker invoker) {        
        return true;    
    }    
    @Override    
    public SofaResponse invoke(FilterInvoker invoker, SofaRequest request) throws SofaRpcException {        
        SofaResponse response = invoker.invoke(request);        
        return response;    
    }
}
```
2. 生效该自定义 Filter 到拦截器链中。这一步具体方式有三种。
  方式1：API方式。该种方式能够生效到指定的 provider 或 consumer 。
```java
// 服务提供者
providerConfig.setFilterRef(Arrays.asList(new CustomFilter()));
// 服务调用者
consumerConfig.setFilterRef(Arrays.asList(new CustomFilter()));
```
  方式2：在类上加上 @Extension 注解+配置扩展文件方式。
```java
@Extension("customer")
public class CustomFilter extends Filter {    
    @Override    
    public boolean needToLoad(FilterInvoker invoker) {        
        return true;    
    }    
    @Override    
    public SofaResponse invoke(FilterInvoker invoker, SofaRequest request) throws SofaRpcException {        
        SofaResponse response = invoker.invoke(request);        
        return response;    
    }
}
```
新建扩展文件 META-INF/services/sofa-rpc/com.alipay.sofa.rpc.filter.Filter 。内容如下：
```
customer=com.alipay.sofa.rpc.custom.CustomFilter
```
编码注入。
```java
// 服务提供者
providerConfig.setFilter(Arrays.asList("customer"));
// 服务调用者
consumerConfig.setFilter(Arrays.asList("customer"));
```
方式三：在类上加上 @Extension 注解+ @AutoActive 注解方式+配扩展文件方式。该种方式利用 @AutoActive 注解代替了上述第二中方式的编码注入步骤，能够生效于所有 provider 或 consumer 。其中 providerSide 参数表示是否生效于服务端， consumerSide 参数表示是否生效于服务端。
```java
@Extension("customer")
@AutoActive(providerSide = true, consumerSide = true)
public class customerFilter extends Filter {
		// ...
}
```

### REST Filter

对于 REST ,我们设计了一个 JAXRSProviderManager 管理器类.在服务端生效,生效时间为服务启动时

```java
com.alipay.sofa.rpc.server.rest.RestServer#registerProvider
```

对于用户自定义的 Filter 类.可以在初始化完成后,调用

```java
com.alipay.sofa.rpc.config.JAXRSProviderManager#registerCustomProviderInstance
```
进行注册,其中自定义的 Filter 遵循 REST 的规范,需要实现如下接口.

```java
javax.ws.rs.container.ContainerResponseFilter
或者
javax.ws.rs.container.ContainerRequestFilter
```

REST server 启动之后,对于裸 SOFARPC 的使用,需要先注册,再启动服务.对于 SOFABoot 环境下的使用,也是类似的过程.具体的写法可以参考

```java
com.alipay.sofa.rpc.server.rest.TraceRequestFilter
com.alipay.sofa.rpc.server.rest.TraceResponseFilter
```

