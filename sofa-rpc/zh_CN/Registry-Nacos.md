## Nacos

SOFARPC 已支持使用 Nacos 作为服务注册中心。假设你已经根据 Nacos 的[快速开始](https://nacos.io/zh-cn/docs/quick-start.html)在本地部署好 Nacos Server，服务发现的端口默认设置在 `8848`。

在 SOFARPC 中使用 Nacos 作为服务注册中心只需要在 application.properties 中加入如下配置即可：
```
com.alipay.sofa.rpc.registry.address=nacos://127.0.0.1:8848
```

如果你直接使用了 SOFARPC，而不是 SOFABoot，需要手动添加 nacos 的依赖，其中 version 为用户想使用的 version。

```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>${version}</version>
</dependency>
```

当前支持 Nacos 的版本：

SOFARPC: [5.5.0](https://github.com/sofastack/sofa-rpc/releases), SOFABoot: [2.5.3](https://github.com/sofastack/sofa-boot/releases/)。

SOFARPC 集成验证 Nacos 服务端版本：[0.6.0](https://github.com/alibaba/nacos/releases/tag/0.6.0)。