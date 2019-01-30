## Custom filter

SOFARPC provides a good extensibility mechanism, which provide SPI capabilities for each module. SOFARPC uses multiple filters to intercept requests and responses. These filters can be customized and extended by users. The execution order of custom filters is after the built-in filters. The procedure is as follows:

### Bolt filter

1 Create a new custom filter.

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
2 The custom filter will be added into the interceptor chain. There are three specific ways to do this step.

- Method 1: In API. In this way, the custom filter can take effect in the specified provider or consumer.

```java
// Service provider
providerConfig.setFilterRef(Arrays.asList(new CustomFilter()));
// Service caller
consumerConfig.setFilterRef(Arrays.asList(new CustomFilter()));

```

- Method 2: Add `@Extension` annotation + configuration extension file to the class.
   
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
Create a new extension file `META-INF/services/sofa-rpc/com.alipay.sofa.rpc.filter.Filter` with the following content:

```
customer=com.alipay.sofa.rpc.custom.CustomFilter
```

Code injection.
```java
// Service provider
providerConfig.setFilter(Arrays.asList("customer"));
// Service caller
consumerConfig.setFilter(Arrays.asList("customer"));
```

- Method 3: Add `@Extension` annotation + `@AutoActive` annotation + configuration extension file to the class. In this way, the code injection step in method 2 is replaced with the `@AutoActive` annotation. The custom filter can take effect in all providers or consumers. The `providerSide` parameter indicates whether it takes effect on the server, and the `consumerSide` parameter indicates whether it takes effect on the client.

```java
@Extension("customer")
@AutoActive(providerSide = true, consumerSide = true)
public class customerFilter extends Filter {
		// ...
}
```