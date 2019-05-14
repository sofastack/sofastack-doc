# 使用 SOFATracer 远程汇报数据到 Zipkin

在本文档将演示如何使用 SOFATracer 集成 Zipkin 进行数据上报展示，本示例[工程地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-zipkin)。

假设你已经基于 SOFABoot 构建了一个简单的 Spring Web 工程，那么可以通过如下步骤进行操作：

> 下面的示例中将分别演示在 SOFABoot/SpringBoot 工程中 以及 非 SOFABoot/SpringBoot 工程中如何使用。


## 依赖引入

### 添加 SOFATracer 依赖

工程中添加 SOFATracer 依赖：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
```

### 配置 Zipkin 依赖

考虑到 Zipkin 的数据上报能力不是 SOFATracer 默认开启的能力，所以期望使用 SOFATracer 做数据上报时，需要添加如下的 Zipkin 数据汇报的依赖：

```xml
 <dependency>
     <groupId>io.zipkin.zipkin2</groupId>
     <artifactId>zipkin</artifactId>
     <version>2.11.12</version>
 </dependency>
 <dependency>
     <groupId>io.zipkin.reporter2</groupId>
     <artifactId>zipkin-reporter</artifactId>
     <version>2.7.13</version>
 </dependency>
```

## 配置文件

在工程的 `application.properties` 文件下添加一个 SOFATracer 要使用的参数，包括`spring.application.name` 用于标示当前应用的名称；`logging.path` 用于指定日志的输出目录。

```properties
# Application Name
spring.application.name=SOFATracerReportZipkin
# logging path
logging.path=./logs
com.alipay.sofa.tracer.zipkin.enabled=true
com.alipay.sofa.tracer.zipkin.baseUrl=http://localhost:9411
```

## 启动 Zipkin 服务端

启动 Zipkin 服务端用于接收 SOFATracer 汇报的链路数据，并做展示。Zipkin Server 的搭建可以[参考此文档](https://zipkin.io/)进行配置和服务端的搭建。

## 运行

可以将工程导入到 IDE 中运行生成的工程里面中的 `main` 方法启动应用，也可以直接在该工程的根目录下运行 `mvn spring-boot:run`，将会在控制台中看到启动日志：

```
2018-05-12 13:12:05.868  INFO 76572 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'SpringMvcSofaTracerFilter' to urls: [/*]
2018-05-12 13:12:06.543  INFO 76572 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/helloZipkin]}" onto public java.util.Map<java.lang.String, java.lang.Object> com.alipay.sofa.tracer.examples.zipkin.controller.SampleRestController.helloZipkin(java.lang.String)
2018-05-12 13:12:07.164  INFO 76572 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
```

可以通过在浏览器中输入 [http://localhost:8080/helloZipkin](http://localhost:8080/helloZipkin) 来访问 REST 服务，结果类似如下：

```json
{
	content: "Hello, SOFATracer Zipkin Remote Report!",
	id: 1,
	success: true
}
```

## 查看 Zipkin 服务端展示

打开 Zipkin 服务端界面，假设我们部署的 Zipkin 服务端的地址是 `http://localhost:9411`，打开 URL 并搜索 `helloZipkin`(由于我们本地访问的地址是 localhost:8080/helloZipkin)，可以看到展示的链路图。

## Spring 工程运行

对于一般的 Spring 工程，我们通常使用 tomcat/jetty 作为 servlet 容器来启动应用。具体工程参考 [在 Spring 工程中使用 SOFATracer](https://github.com/glmapper/tracer-zipkin-plugin-demo)



