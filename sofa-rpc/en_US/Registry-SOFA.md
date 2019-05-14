## SOFARegistry

SOFARPC already supports using SOFARegistry as a service registry. Suppose you have deployed SOFARegistry Server locally according to SOFARegistry's [Quick Start] (https://www.sofastack.tech/sofa-registry/docs/Server-QuickStart), and the service discovery port is set to `9603` by default.

To use SOFARegistry as a service registry in SOFARPC, you only need to add the following configuration to `application.properties`:
```
com.alipay.sofa.rpc.registry.address=sofa://127.0.0.1:9603
```

The current version of SOFARegistry is supported:

SOFARPC: [5.5.2](https://github.com/sofastack/sofa-rpc/releases), SOFABoot: [2.6.3](https://github.com/sofastack/sofa-boot/releases/)。

Because of the time of SOFABoot, users need to specify the version of rpc starter.

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-starter</artifactId>
    <version>5.5.2</version>
</dependency>
```

SOFARPC integration verification SOFARegistry server version:[5.2.0](https://github.com/sofastack/sofa-registry/releases/tag/5.2.0)。
