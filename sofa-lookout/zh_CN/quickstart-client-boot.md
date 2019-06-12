# 快速开始

该项目演示了如何在 SOFABoot 中使用 SOFALookout 并且对接到 Spring Boot 的 Actuator 中。如果想要对接到 Prometheus 上或者其他的 Registry 中，请参考 Registry 一节。

## 新建 SpringBoot（或 SofaBoot ）项目

新建一个 Spring Boot 的应用（如果是 SOFABoot 工程按照 [SOFABoot 文档--依赖管理](https://www.sofastack.tech/sofa-boot/docs/DependencyManagement)中的方式引入 SOFABoot 即可）。

## 引入 Lookout 的 Starter 依赖

在 `pom.xml` 中引入以下依赖即可：

```xml
<dependency>
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-sofa-boot-starter</artifactId>
</dependency>
```

如果 Spring Boot 项目需指定[版本](http://mvnrepository.com/artifact/com.alipay.sofa.lookout/lookout-sofa-boot-starter)。


## 新建一个 Metrics 指标

在完成依赖的引入之后，然后可以在 Spring Boot 中的启动类中，加入如下的方法：

```java
@Autowired
private Registry registry;

@PostConstruct
public void init() {
    Counter counter = registry.counter(registry.createId("http_requests_total").withTag("instant", NetworkUtil.getLocalAddress().getHostName()));
    counter.inc();
}
```

上面的代码中直接通过 `@Autowired` 注入了一个 Registry 的字段，通过这个 Registry 的字段，我们就可以创建对应的 Counter，然后通过修改这个 Counter 的数据来生成 SOFALookout 的 Metrics 的指标。

## 添加配置项

在 SOFABoot 项目中，需要增加一个应用名的配置项：`spring.application.name=xxx`。

## 与 Spring Boot Actuator 对接

新增了一个指标之后，我们可以选择对接到 Spring Boot Actuator 上，要对接到 Spring Boot Actuator 上面，需要添加如下的依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

添加如上的依赖之后，我们在本地启动应用，访问 `http://localhost:8080/metrics`，就可以看到前面添加的指标，如下：

```
"http_requests_total.instant-MacBook-Pro-4.local": 1,
```

以上的 QuickStart 的代码在: [lookout-client-samples-boot](https://github.com/sofastack/sofa-lookout/tree/master/samples/metrics/client/lookout-client-samples-boot)，大家可以下载作为参考。
