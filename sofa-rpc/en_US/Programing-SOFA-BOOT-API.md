# SOFABoot

## Use API

SOFABoot provides a set of programming APIs for RPC service publishing and reference. It is convenient to publish and reference RPC services directly in the code. Similar to Spring's ApplicationContextAware, in order to use the programming API, you first need to implement the ClientFactoryAware interface to get the programming component API:

```java
public class ClientFactoryBean implements ClientFactoryAware {
    private ClientFactory clientFactory;

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

With DirectService as an example, see how to use the clientFactory to publish an RPC service through the programming API:

```java
ServiceClient serviceClient = clientFactory.getClient(ServiceClient.class);

ServiceParam serviceParam = new ServiceParam();
serviceParam.setInterfaceType(DirectService.class);
serviceParam.setInstance(new DirectServiceImpl());
            
List<BindingParam> params = new ArrayList<BindingParam>();
BindingParam serviceBindingParam = new BoltBindingParam();
params.add(serviceBindingParam);
serviceParam.setBindingParams(params);

serviceClient.service (serviceParam);
```

In the code above:

1. First, get the `ServiceClient` object through the `clientFactory`.
2. Then, construct the `ServiceParam` object, which contains the parameters required to publish the service, and use the `setInstance` method to set the object to be published as an RPC service, `setInterfaceType` to set the interface of the service.
3. Finally, call the service method of `ServiceClient` to publish an RPC service.

The code that references the RPC service through the programming API is similar:

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);
ReferenceParam<DirectService> referenceParam = new ReferenceParam<DirectService>();
referenceParam.setInterfaceType(DirectService.class);

BindingParam refBindingParam = new BoltBindingParam();
referenceParam.setBindingParam(refBindingParam);

DirectService proxy = referenceClient.reference(referenceParam);
proxy.sayDirect("hello");
```

Likewise, to reference an RPC service, the code simply needs to get a `ReferenceClient` from the `ClientFactory` and then construct a `ReferenceParam` similar to publishing a service, next set up the service interface, and finally call the ReferenceClient's reference method.