# SOFATracer 集成 SpringMVC

在本文档将演示如何使用 SOFATracer 对 SpringMVC 进行埋点，本示例[工程地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-springmvc)。

假设你已经基于 SOFABoot 构建了一个简单的 Spring Web 工程，那么可以通过如下步骤进行操作：

## 依赖引入

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
```

## 工程配置

在工程的 `application.properties` 文件下添加 SOFATracer 要使用的参数，包括`spring.application.name` 用于标示当前应用的名称；`logging.path` 用于指定日志的输出目录。

```properties
# Application Name
spring.application.name=SOFATracerSpringMVC
# logging path
logging.path=./logs
```

## 添加一个提供 RESTful 服务的 Controller

在工程代码中，添加一个简单的 Controller，例如：

```java
@RestController
public class SampleRestController {

    private static final String TEMPLATE = "Hello, %s!";
    private final AtomicLong    counter  = new AtomicLong();
    /**
     * http://localhost:8080/springmvc
     * @param name name
     * @return map
     */
    @RequestMapping("/springmvc")
    public Map<String, Object> springmvc(@RequestParam(value = "name", defaultValue = "SOFATracer SpringMVC DEMO") String name) {
        Map<String, Object> resultMap = new HashMap<String, Object>();
        resultMap.put("success", true);
        resultMap.put("id", counter.incrementAndGet());
        resultMap.put("content", String.format(TEMPLATE, name));
        return resultMap;
    }
}
```

## 运行

启动 SOFABoot 应用，将会在控制台中看到启动打印的日志：

```
2018-05-11 11:55:11.932  INFO 66490 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'SpringMvcOpenTracingFilter' to urls: [/*]
2018-05-11 11:55:13.961  INFO 66490 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-05-11 11:55:13.970  INFO 66490 --- [           main] c.a.s.t.e.springmvc.DemoApplication      : Started DemoApplication in 8.361 seconds (JVM running for 9.34)
```

可以通过在浏览器中输入 [http://localhost:8080/springmvc](http://localhost:8080/springmvc) 来访问 REST 服务，结果类似如下：

```json
{
	content: "Hello, SOFATracer SpringMVC DEMO!",
	id: 1,
	success: true
}
```

## 查看日志

在上面的 `application.properties` 里面，我们配置的日志打印目录是 `./logs` 即当前应用的根目录（我们可以根据自己的实践需要配置），在当前工程的根目录下可以看到类似如下结构的日志文件：

```
./logs
├── spring.log
└── tracelog
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log

```

通过访问 [http://localhost:8080/springmvc](http://localhost:8080/springmvc) SOFATracer 会记录每一次访问的摘要日志，可以打开 `spring-mvc-digest.log` 看到具体的输出内容，而对于每一个输出字段的含义可以 [参考这里](https://www.sofastack.tech/sofa-tracer/docs/SpringMVC)。

```json
{"time":"2018-05-17 22:20:34.279","local.app":"SOFATracerSpringMVC","traceId":"0a0fe9391526566833985100139443","spanId":"0","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":69,"time.cost.milliseconds":284,"current.thread.name":"http-nio-8080-exec-1","baggage":""}
```
