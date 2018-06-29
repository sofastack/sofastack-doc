## 注册中心选择

SOFABoot RPC Starter 为用户提供多种注册中心选择和方便的配置。目前 bolt ， rest ， dubbo 都支持 Zookeeper 作为注册中心。另外 bolt ， rest 支持本地文件系统作为注册中心，该种模式一般用于测试。

### Zookeeper

使用 Zookeeper 作为服务注册中心只需要在 application.properties 中如下配置即可：
```
com.alipay.sofa.rpc.registry.address=zookeeper://127.0.0.1:2181
```
SOFABoot RPC 也提供一个缓存文件(目前暂不支持)，当 Zookeeper 不可用时，使用该缓存文件进行服务发现。配置该缓存文件的方式如下：
```
com.alipay.sofa.rpc.registry.address=zookeeper://xxx:2181?file=/home/admin/registry
```
如上 file 参数的值 `/home/admin/registry` 就是缓存文件。如果不显式配置缓存文件地址，那么 SOFABoot RPC 会提供一个默认的缓存文件地址：
```
System.getProperty("user.home") + "/localFileRegistry/localRegistry.reg";
```

### 本地文件

使用本地文件作为服务注册中心在 application.properties 中如下配置即可：
```
com.alipay.sofa.rpc.registry.address=local:/home/admin/registry
```
其中 `/home/admin/registry` 就是使用的本地文件的目录。