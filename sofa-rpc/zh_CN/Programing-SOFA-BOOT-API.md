# SOFABoot

## API 方式

SOFABoot 为 RPC 服务的发布和引用提供了一套编程 API 方式，方便直接在代码中发布和引用 RPC 服务，与 Spring 的 ApplicationContextAware 类似，为使用编程 API 方式，首先需要实现 ClientFactoryAware 接口获取编程组件 API：

```java
public class ClientFactoryBean implements ClientFactoryAware {
    private ClientFactory clientFactory;

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

以 DirectService 为例，看下如何使用 clientFactory 通过编程 API 方式发布 RPC 服务：

```java
ServiceClient serviceClient = clientFactory.getClient(ServiceClient.class);

ServiceParam serviceParam = new ServiceParam();
serviceParam.setInterfaceType(DirectService.class);
serviceParam.setInstance(new DirectServiceImpl());
            
List<BindingParam> params = new ArrayList<BindingParam>();
BindingParam serviceBindingParam = new BoltBindingParam();
params.add(serviceBindingParam);
serviceParam.setBindingParams(params);

serviceClient.service(serviceParam);
```

上面的代码中

1.  首先通过 clientFactory 获得 ServiceClient 对象
2.  然后构造 ServiceParam 对象，ServiceParam 对象包含发布服务所需参数，通过 setInstance 方法来设置需要被发布成 RPC 服务的对象，setInterfaceType 来设置服务的接口
3.  最后，调用 ServiceClient 的 service 方法，发布一个 RPC 服务

通过编程 API 方式引用 RPC 服务的代码也是类似的：

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);
ReferenceParam<DirectService> referenceParam = new ReferenceParam<DirectService>();
referenceParam.setInterfaceType(DirectService.class);

BindingParam refBindingParam = new BoltBindingParam();
referenceParam.setBindingParam(refBindingParam);

DirectService proxy = referenceClient.reference(referenceParam);
proxy.sayDirect("hello");
```

同样，引用一个 RPC 服务只需从 ClientFactory 中获取一个 ReferenceClient ，然后和发布一个服务类似，构造出一个 ReferenceParam，然后设置好服务的接口，最后调用 ReferenceClient 的 reference 方法即可。
