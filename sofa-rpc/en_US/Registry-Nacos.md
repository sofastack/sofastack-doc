## Nacos

SOFARPC already supports using Nacos as a service registry. Suppose you have deployed Nacos Server locally according to Nacos's [Quick Start] (https://nacos.io/zh-cn/docs/quick-start.html), and the service discovery port is set to `8848` by default.

To use Nacos as a service registry in SOFARPC, you only need to add the following configuration to `application.properties`:
```
com.alipay.sofa.rpc.registry.address=nacos://127.0.0.1:8848
```

If you use SOFARPC directly, not SOFABoot, you need to add dependency of nacos, notice that version is what you want to use in your project.

```xml
<dependency>
    <groupId>com.alibaba.nacos</groupId>
    <artifactId>nacos-client</artifactId>
    <version>${version}</version>
</dependency>
```


The current version of Nacos is supported:

SOFARPC: [5.5.0](https://github.com/sofastack/sofa-rpc/releases), SOFABoot: [2.5.3](https://github.com/sofastack/sofa-boot/releases/)。

SOFARPC integration verification Nacos server version:[0.6.0](https://github.com/alibaba/nacos/releases/tag/0.6.0)。