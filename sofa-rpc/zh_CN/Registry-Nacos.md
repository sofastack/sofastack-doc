## Nacos

本地测试 Nacos 作为服务注册中心的场景下，假设你已经根据 Nacos 的快速开始在本地部署好 Nacos Server，服务发现的端口默认设置在 `8848`。

在 SOFA RPC 中使用 Nacos 作为服务注册中心只需要在 application.properties 中如下配置即可：
```
com.alipay.sofa.rpc.registry.address=nacos://127.0.0.1:8848
```

支持 Nacos 的版本：

SOFA RPC: 5.5.0+, SOFA Boot: 2.5.3+。