# 客户端快速开始
##  SpringBoot (SofaBoot)项目

- 在应用中加入 client 的 starter

```xml
<dependency>
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-sofa-boot-starter</artifactId>
    <version>${lookout.client.version}</version>
</dependency>
```
该 starter 依赖的 lookout-client 默认依赖了 lookout-reg-server 模块（支持向 lookout server 上报 metrics 数据），如果希望使用其他类型注册表(比如 lookout-reg-prometheus)，那么再加上对应依赖即可。

- 如何使用

在 SpringBoot 加入 lookout-sofa-boot-starter 后,即可以在使用的地方自动注入 com.alipay.lookout.api.Registry 实例即可：
```java
@Autowired
private Registry registry;

//实际方法体...
Counter counter = registry.counter(registry.createId("http_requests_total").withTag("instant", NetworkUtil.getLocalAddress().getHostName()));
counter.inc();
```
更多细节参考样例工程: lookout-client-samples-boot

- 配置项要求

在 SpringBoot 项目中，配置文件中必须指定应用名：`spring.application.name=xx`。更多 Lookout Client 配置项，请参考配置说明部分。