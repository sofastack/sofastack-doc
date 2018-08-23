## 自定义 Router
SOFA-RPC 中对服务地址的选择也抽象为了一条处理链，由每一个 Router 进行处理。同 Filter 一样， SOFA-RPC 对 Router 提供了同样的扩展能力。
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
新建扩展文件 META-INF/services/sofa-rpc/com.alipay.sofa.rpc.client.Router 。内容如下：
```
customerRouter=com.alipay.sofa.rpc.custom.CustomRouter
```
如上自定义了一个 CustomerRouter ，生效于所有消费者。其中 init 参数 ConsumerBootstrap 是引用服务的包装类，能够拿到 ConsumerConfig ，代理类，服务地址池等对象。 needToLoad 表示是否生效该 Router ， route 方法即筛选地址的方法。
