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


### Zookeeper Auth 支持

当用户需要对发布和消费服务，进行权限认证的时候，可以通过在操作 zookeeper 时，指定对应的目录和账号密码来进行读写。这样只有使用了相同密码的
服务方或者消费方才能进行读写。


#### SOFARPC API 支持

在构造注册中心的时候，将Auth添加上

```java
parameters.put("scheme", "digest");
//如果存在多个认证信息，则在参数形式为为user1:passwd1,user2:passwd2
parameters.put("addAuth", "sofazk:rpc1");

registryConfig = new RegistryConfig()
.setProtocol("zookeeper")
.setAddress("127.0.0.1:2181/authtest")
.setParameters(parameters);
```

之后其他没有使用正确auth的，将无法访问authtest目录


#### XML 方式支持

如下使用即可
```xml
com.alipay.sofa.rpc.registry.address=zookeeper://xxx:2181?file=/home/admin/registry&scheme=digest&addAuth=sofazk:rpc1
```
