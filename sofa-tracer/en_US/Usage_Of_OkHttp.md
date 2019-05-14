# OkHttp Integration

In this document will demonstrate how to use SOFATracer to track of OkHttp, this example [address] (https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-okhttp).

Assuming you have built a simple Spring Web project based on SOFABoot, Then you can be operated by the following steps:

## Dependency introduction

```xml
<!-- SOFATracer dependency -->
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
<!-- okhttp dependency -->
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>3.12.1</version>
</dependency>
```
## Project Configuration

Then, add the parameters to be used by SOFATracer in the project's `application.properties` file, including `spring.application.name` that indicates the name of the current application and `logging.path` that specifies the log output directory.

```properties
# Application Name
spring.application.name=HttpClientDemo
# logging path
logging.path=./logs
# port
server.port=8081
```

## Add a Controller that provides RESTful services

In the project, provide a simple Controller, for example:

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
## Construct OkHttp to initiate a call to the RESTful service above

The code example is as follows:

* Construct the OkHttp Client instance:

```java
OkHttpClientInstance httpClient = new OkHttpClientInstance();
String httpGetUrl = "http://localhost:8081/okhttp?name=sofa";
String responseStr = httpClient.executeGet(httpGetUrl);
```

## Run

Start the SOFABoot app and see the log in the console as follows:

```
2019-04-12 13:38:09.896  INFO 51193 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2019-04-12 13:38:09.947  INFO 51193 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8081 (http)
2019-04-12 13:38:09.952  INFO 51193 --- [           main] c.a.s.t.e.okhttp.OkHttpDemoApplication   : Started OkHttpDemoApplication in 3.314 seconds (JVM running for 4.157)
```

When there is a log similar to the following, the call to OkHttp is successful:

```
2019-04-12 13:38:10.205  INFO 51193 --- [           main] c.a.s.t.e.okhttp.OkHttpDemoApplication   : Response is {"count":1,"name":"sofa"}
```

## View log

In the `application.properties`, the log printing directory we configured is `./logs`, which is the root directory of the current application (we can configure it based on actual situation). In the root directory, you can see log files in the structure similar to the followings:

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

In the example, you can see logs like the following in okhttp-digest. 

```json
{"time":"2019-04-12 13:38:10.187","local.app":"OkHttpDemo","traceId":"0a0fe85a1555047489980100151193","spanId":"0","request.url":"http://localhost:8081/okhttp?name=sofa","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":0,"time.cost.milliseconds":207,"current.thread.name":"main","remote.app":"","baggage":""}
```