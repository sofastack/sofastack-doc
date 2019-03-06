## Zookeeper

To use Zookeeper as service registry center, you only need to configure it in `application.properties` as follows:
```
com.alipay.sofa.rpc.registry.address=zookeeper://127.0.0.1:2181
```

Note: Considering the real-time nature of the service, the following features are not supported currently.

SOFABoot RPC also provides a cache file (not supported  currently), which is used for service discovery when ZooKeeper is not available. The way to configure this cache file is as follows:
```
com.alipay.sofa.rpc.registry.address=zookeeper://xxx:2181?file=/home/admin/registry
```

### Zookeeper Auth 

When users need to auth the providers and consumers, they can use a auth key to write or read the dictionary normally,
only when they use the same key, zookeeper server will process these requests.

#### SOFARPC API Usage

If you use SOFARPC API directly, you can add two parameters to registry config.

```java
parameters.put("scheme", "digest");
//if there was multi auth infos, you need to set the value as user1:passwd1,user2:passwd2
parameters.put("addAuth", "sofazk:rpc1");

registryConfig = new RegistryConfig()
.setProtocol("zookeeper")
.setAddress("127.0.0.1:2181/authtest")
.setParameters(parameters);
```
then if another provider or consumer use a different auth info, they will not access these providers or consumers.

#### XML Usage

You only need to set it in `application.properties`    
```xml
com.alipay.sofa.rpc.registry.address=zookeeper://xxx:2181?file=/home/admin/registry&scheme=digest&addAuth=sofazk:rpc1
```
