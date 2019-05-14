# SOFATracer 集成 RestTemplate

在本文档将演示如何使用 SOFATracer 对 RestTemplate 进行埋点，本示例[工程地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-resttemplate)。

假设你已经基于 SOFABoot 构建了一个简单的 Spring Web 工程，那么可以通过如下步骤进行操作：

## 依赖引入

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
```

## 工程配置

在工程的 `application.properties` 文件下添加 SOFATracer 要使用的参数，包括 `spring.application.name` 用于标示当前应用的名称；`logging.path` 用于指定日志的输出目录。

```properties
# Application Name
spring.application.name=TestTemplateDemo
# logging path
logging.path=./logs
```

## 添加一个提供 RESTful 服务的 Controller

在工程代码中，添加一个简单的 Controller，例如：

```java
@RestController
public class SampleController {
    private final AtomicLong counter = new AtomicLong(0);
    @RequestMapping("/rest")
    public Map<String, Object> rest() {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("count", counter.incrementAndGet());
        return map;
    }

    @RequestMapping("/asyncrest")
    public Map<String, Object> asyncrest() throws InterruptedException {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("count", counter.incrementAndGet());
        Thread.sleep(5000);
        return map;
    }
}
```

## 以 API 方式构造 RestTemplate 发起一次对上文的 RESTful 服务的调用

* 构造 RestTemplate 同步调用实例

```java
RestTemplate restTemplate = SofaTracerRestTemplateBuilder.buildRestTemplate();
ResponseEntity<String> responseEntity = restTemplate.getForEntity(
            "http://sac.alipay.net:8080/rest", String.class);
```

* 构造 RestTemplate 异步调用实例

```java
AsyncRestTemplate asyncRestTemplate = SofaTracerRestTemplateBuilder
    .buildAsyncRestTemplate();
ListenableFuture<ResponseEntity<String>> forEntity = asyncRestTemplate.getForEntity(
            "http://sac.alipay.net:8080/asyncrest", String.class);
```

## 以自动注入的方式获取 RestTemplate

```java
@Autowired
RestTemplate             restTemplate;
```

## 运行

启动 SOFABoot 应用，将会在控制台中看到启动打印的日志：

```json
2018-10-24 10:45:28.683  INFO 5081 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-10-24 10:45:28.733  INFO 5081 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-10-24 10:45:28.736  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Started RestTemplateDemoApplication in 2.163 seconds (JVM running for 3.603)
```

调用成功：

```json
2018-10-24 10:45:28.989  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Response is {"count":1}
2018-10-24 10:45:34.014  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Async Response is {"count":2}
2018-10-24 10:45:34.014  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : test finish .......
```

## 查看日志

在上面的 `application.properties` 里面，我们配置的日志打印目录是 `./logs` 即当前应用的根目录（我们可以根据自己的实践需要进行配置），在当前工程的根目录下可以看到类似如下结构的日志文件：

```
./logs
├── spring.log
└── tracelog
    ├── resttemplate-digest.log
    ├── resttemplate-stat.log
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log
```

示例中通过构造两个 RestTemplate（一个同步一个异步） 发起对同一个 RESTful 服务的调用，调用完成后可以在 restTemplate-digest.log 中看到类似如下的日志：

* 摘要日志

```json
{"time":"2018-10-24 10:45:28.977","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912878910015081","spanId":"0","request.url":"http://sac.alipay.net:8080/rest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":188,"current.thread.name":"main","remote.app":"","baggage":""}
{"time":"2018-10-24 10:45:34.013","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912900410025081","spanId":"0","request.url":"http://sac.alipay.net:8080/asyncrest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":5009,"current.thread.name":"SimpleAsyncTaskExecutor-1","remote.app":"","baggage":""}
```

* 统计日志

```json
{"time":"2018-10-24 10:46:28.769","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/asyncrest"},"count":1,"total.cost.milliseconds":5009,"success":"true","load.test":"F"}
{"time":"2018-10-24 10:46:28.770","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/rest"},"count":1,"total.cost.milliseconds":188,"success":"true","load.test":"F"}
```