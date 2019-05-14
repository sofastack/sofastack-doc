# SOFATracer 集成 HttpClient

在本文档将演示如何使用 SOFATracer 对 HttpClient 进行埋点，本示例[工程地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-httpclient)。

假设你已经基于 SOFABoot 构建了一个简单的 Spring Web 工程，那么可以通过如下步骤进行操作：

## 依赖引入

```xml
<!-- SOFATracer 依赖 -->
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
<!-- HttpClient 依赖 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <!-- 版本 4.5.X 系列 -->
    <version>4.5.3</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpasyncclient</artifactId>
    <!-- 版本 4.X 系列 -->
    <version>4.1.3</version>
</dependency>
```
## 工程配置

在工程的 `application.properties` 文件下添加 SOFATracer 要使用的参数，包括`spring.application.name` 用于标示当前应用的名称；`logging.path` 用于指定日志的输出目录。

```properties
# Application Name
spring.application.name=HttpClientDemo
# logging path
logging.path=./logs
```

## 添加一个提供 RESTful 服务的 Controller

在工程代码中，添加一个简单的 Controller，例如：

```java
@RestController
public class SampleRestController {

    private final AtomicLong counter = new AtomicLong(0);

    /**
     * Request http://localhost:8080/httpclient?name=
     * @param name name
     * @return Map of Result
     */
    @RequestMapping("/httpclient")
    public Map<String, Object> greeting(@RequestParam(value = "name", defaultValue = "httpclient") String name) {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("count", counter.incrementAndGet());
        map.put("name", name);
        return map;
    }
}
```
## 构造 HttpClient 发起一次对上文的 RESTful 服务的调用

代码示例如下：

* 构造 HttpClient 同步调用实例：

```java
HttpClientBuilder httpClientBuilder = HttpClientBuilder.create();
//SOFATracer
SofaTracerHttpClientBuilder.clientBuilder(httpClientBuilder);
CloseableHttpClient httpClient = httpClientBuilder.setConnectionManager(connManager).disableAutomaticRetries()
                .build();
```

* 构造 HttpClient 异步调用实例：

```java
RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(6000).setConnectTimeout(6000).setConnectionRequestTimeout(6000).build();
HttpAsyncClientBuilder httpAsyncClientBuilder = HttpAsyncClientBuilder.create();
//tracer
SofaTracerHttpClientBuilder.asyncClientBuilder(httpAsyncClientBuilder);
CloseableHttpAsyncClient asyncHttpclient = httpAsyncClientBuilder.setDefaultRequestConfig(requestConfig).build();
```

通过 SofaTracerHttpClientBuilder(clientBuilder 方法构造同步，asyncClientBuilder 方法构造异步) 构造的 HttpClient 在发起对上文的 RESTful 服务调用的时候，就会埋点 SOFATracer 的链路的数据。

## 运行

启动 SOFABoot 应用，在控制台中看到启动打印的日志如下：

```
2018-09-27 20:31:21.465  INFO 33277 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-09-27 20:31:21.599  INFO 33277 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-09-27 20:31:21.608  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Started HttpClientDemoApplication in 5.949 seconds (JVM running for 6.573)
```

当有类似如下的日志时，说明 HttpClient 的调用成功：

```
2018-09-27 20:31:22.336  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Response is {"count":1,"name":"httpclient"}
2018-09-27 20:31:22.453  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Async Response is {"count":2,"name":"httpclient"}
```

## 查看日志

在上面的 `application.properties` 里面，我们配置的日志打印目录是 `./logs` 即当前应用的根目录（我们可以根据自己的实践需要进行配置），在当前工程的根目录下可以看到类似如下结构的日志文件：

```
./logs
├── spring.log
└── tracelog
    ├── httpclient-digest.log
    ├── httpclient-stat.log
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log

```

示例中通过构造两个 HttpClient（一个同步一个异步） 发起对同一个 RESTful 服务的调用，调用完成后可以在 httpclient-digest.log 中看到类似如下的日志，而对于每一个输出字段的含义可以看 SOFATracer 的说明文档：

```json
{"time":"2018-09-27 20:31:22.328","local.app":"HttpClientDemo","traceId":"0a0fe8801538051482054100133277","spanId":"0","request.url":"http://localhost:8080/httpclient","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":-1,"time.cost.milliseconds":274,"current.thread.name":"main","remote.app":"","baggage":""}
{"time":"2018-09-27 20:31:22.443","local.app":"HttpClientDemo","traceId":"0a0fe8801538051482410100233277","spanId":"0","request.url":"http://localhost:8080/httpclient","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":-1,"time.cost.milliseconds":33,"current.thread.name":"I/O dispatcher 1","remote.app":"","baggage":""}
```