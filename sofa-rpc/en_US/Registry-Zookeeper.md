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