## Zookeeper

使用 Zookeeper 作为服务注册中心只需要在 application.properties 中如下配置即可：
```
com.alipay.sofa.rpc.registry.address=zookeeper://127.0.0.1:2181
```

注意：考虑掉服务的实时性，以下特性暂不支持

SOFABoot RPC 也提供一个缓存文件(目前暂不支持)，当 Zookeeper 不可用时，使用该缓存文件进行服务发现。配置该缓存文件的方式如下：
```
com.alipay.sofa.rpc.registry.address=zookeeper://xxx:2181?file=/home/admin/registry
```