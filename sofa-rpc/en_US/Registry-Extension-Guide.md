# Registry center extension guide

If you want to extend a registry center, you should take a look at the abstract classes of the registry center.

```java
package com.alipay.sofa.rpc.registry;

@Extensible(singleton = false)
public abstract class Registry implements Initializable, Destroyable {
    public abstract boolean start();
    public abstract void register(ProviderConfig config);
    public abstract void unRegister(ProviderConfig config);
    public abstract void batchUnRegister(List<ProviderConfig> configs);
    public abstract List<ProviderGroup> subscribe(ConsumerConfig config);
    public abstract void unSubscribe(ConsumerConfig config);
    public abstract void batchUnSubscribe(List<ConsumerConfig> configs);
}
```

You can see the main necessary interfaces.

- [ ] Start the registry client and maintain the connection;
- [ ] Destroy the registry client and release resources;
- [ ] Publish service and cache publish information;
- [ ] Unpublish service and delete cache;
- [ ] Subscribe to service list, return data synchronously or asynchronously, and receive notifications upon changes
- [ ] Unsubscribe service list and delete cache

Other interfaces:

- [ ] When the registry center node is disconnected, the local call is not affected.
- [ ] Switch from one disconnected registry center node to another one by itself.
- [ ] After switching to another registry center node, the system resumes the registration and subscription information automatically.
- [ ] The registry data is cached to the local file. Even if no registry center node is connected, the service provider and caller can restart and call normally.