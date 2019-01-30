## Custom router
The route service address in SOFARPC is abstracted into a processing chain, and is processed by each router. Like filter, SOFARPC provides the same extensibility for router.

```java
@Extension(value = "customerRouter")
@AutoActive(consumerSide = true)
public class CustomerRouter extends Router {
   
    @Override
    public void init(ConsumerBootstrap consumerBootstrap) {
        
    }
    @Override
    public boolean needToLoad(ConsumerBootstrap consumerBootstrap) {
       return true;
    }
    @Override
    public List<ProviderInfo> route(SofaRequest request, List<ProviderInfo> providerInfos) {
        return providerInfos;
    }
```

Create a extension file `META-INF/services/sofa-rpc/com.alipay.sofa.rpc.client.Router` with the following content:

```
customerRouter=com.alipay.sofa.rpc.custom.CustomRouter
```

This file customized a `CustomerRouter`, which takes effect in all consumers. The parameter `ConsumerBootstrap` in init method is a wrapper class of the referenced service, and can get objects such as `ConsumerConfig`, proxy class, and service address pool. `needToLoad` indicates whether the Router is valid, and the route method is the method for filtering addresses.