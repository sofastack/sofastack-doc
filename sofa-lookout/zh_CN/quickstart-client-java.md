# 客户端快速开始

## 普通 Java 项目

在应用中加入 client 的 Maven 依赖

```xml
<dependency>    
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-client</artifactId>
    <version>${lookout.client.version}</version>
</dependency>
```

lookout-client 默认依赖了 lookout-reg-server 模块（支持向 lookout server 上报 metrics 数据），如果希望使用其他类型注册表(比如 lookout-reg-prometheus)，那么再加上对应依赖即可。

开始使用 SOFALookout 的 Client 之前，首先需要构建一个全局的客户端实例（ `com.alipay.lookout.client.DefaultLookoutClient` ）

```java
LookoutConfig lookoutConfig = new LookoutConfig();

DefaultLookoutClient client = new DefaultLookoutClient("appName");
//选择构建需要使用的 Registry(如果多个注册表类型，建议使用同一 lookoutConfig 实例，便于集中管理)
LookoutRegistry lookoutRegistry = new LookoutRegistry(lookoutConfig);
//客户端可以后置添加 registry 实例(至少要加一个)
client.addRegistry(lookoutRegistry);
//(可选)对已加入或后续加入的客户端的 registry 实例，统一注册扩展模块的 metrics
client.registerExtendedMetrics();
```
然后通过客户端拿取 Registry 实例，进行使用：

```java
//该注册表是个“组合”型的注册表
Registry registry = client.getRegistry();
//demo
Id id = registry.createId("http_requests_total");
Counter counter = registry.counter(id);
counter.inc();
```
客户端的使用，可以详细参考[样例工程](https://github.com/sofastack/sofa-lookout/tree/master/samples/metrics/client/lookout-client-samples-java)。


