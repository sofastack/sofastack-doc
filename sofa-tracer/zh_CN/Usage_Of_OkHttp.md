# SOFATracer 集成 OkHttp

在本文档将演示如何使用 SOFATracer 对 OkHttp 进行埋点，本示例[工程地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-okhttp)。

假设你已经基于 SOFABoot 构建了一个简单的 Spring Web 工程，那么可以通过如下步骤进行操作：

## 依赖引入

```xml
<!-- SOFATracer 依赖 -->
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
<!-- okhttp 依赖 -->
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>3.12.1</version>
</dependency>
```
## 工程配置

在工程的 `application.properties` 文件下添加 SOFATracer 要使用的参数，包括`spring.application.name` 用于标示当前应用的名称；`logging.path` 用于指定日志的输出目录。

```properties
# Application Name
spring.application.name=OkHttpClientDemo
# logging path
logging.path=./logs
# port
server.port=8081
```

## 添加一个提供 RESTful 服务的 Controller

在工程代码中，添加一个简单的 Controller，例如：

```java
@RestController
public class SampleRestController {

    private final AtomicLong counter = new AtomicLong(0);

    /**
     * Request http://localhost:8081/okhttp?name=sofa
     * @param name name
     * @return Map of Result
     */
    @RequestMapping("/okhttp")
    public Map<String, Object> greeting(@RequestParam(value = "name", defaultValue = "okhttp") String name) {
        Map<String, Object> map = new HashMap<>();
        map.put("count", counter.incrementAndGet());
        map.put("name", name);
        return map;
    }
}
```
## 构造 OkHttp 发起一次对上文的 RESTful 服务的调用

代码示例如下：

* 构造 OkHttp Client 调用实例：

```java
OkHttpClientInstance httpClient = new OkHttpClientInstance();
String httpGetUrl = "http://localhost:8081/okhttp?name=sofa";
String responseStr = httpClient.executeGet(httpGetUrl);
```

## 运行

启动 SOFABoot 应用，在控制台中看到启动打印的日志如下：

```
2019-04-12 13:38:09.896  INFO 51193 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-04-12 13:38:09.947  INFO 51193 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8081 (http)
2019-04-12 13:38:09.952  INFO 51193 --- [           main] c.a.s.t.e.okhttp.OkHttpDemoApplication   : Started OkHttpDemoApplication in 3.314 seconds (JVM running for 4.157)
```

当有类似如下的日志时，说明 OkHttp 的调用成功：

```
2019-04-12 13:38:10.205  INFO 51193 --- [           main] c.a.s.t.e.okhttp.OkHttpDemoApplication   : Response is {"count":1,"name":"sofa"}
```

## 查看日志

在上面的 `application.properties` 里面，我们配置的日志打印目录是 `./logs` 即当前应用的根目录（我们可以根据自己的实践需要进行配置），在当前工程的根目录下可以看到类似如下结构的日志文件：

```
./logs
├── spring.log
└── tracelog
    ├── okhttp-digest.log
    ├── okhttp-stat.log
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log
```

示例中通过构造 OkHttp 对象发起 RESTful 服务的调用，调用完成后可以在 okhttp-digest.log 中看到类似如下的日志:

```json
{"time":"2019-04-12 13:38:10.187","local.app":"OkHttpDemo","traceId":"0a0fe85a1555047489980100151193","spanId":"0","request.url":"http://localhost:8081/okhttp?name=sofa","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":0,"time.cost.milliseconds":207,"current.thread.name":"main","remote.app":"","baggage":""}
```