## 本地文件

使用本地文件作为服务注册中心在 application.properties 中如下配置即可：
```
com.alipay.sofa.rpc.registry.address=local:///home/admin/registry/localRegistry.reg
```

其中 `/home/admin/registry/localRegistry.reg` 就是使用的本地文件的目录。

对于 windows 用户，则以上地址类似：

```
com.alipay.sofa.rpc.registry.address=local://c://users/localRegistry.reg
```