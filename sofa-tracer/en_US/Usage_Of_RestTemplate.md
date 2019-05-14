# RestTemplate Integration

In this document will demonstrate how to use SOFATracer to track of RestTemplate, this example [address] (https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-resttemplate)).

Assuming you have built a simple Spring Web project based on SOFABoot, Then you can be operated by the following steps:

## Introduce dependency 

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
    <!-- SOFABoot version unified management -->
</dependency>
```
## Project Configuration

Then, add the parameters to be used by SOFATracer in the project's `application.properties` file, including `spring.application.name` that indicates the name of the current application and `logging.path` that specifies the log output directory.

```properties
# Application Name
spring.application.name=SOFATracerSpringMVC
# logging path
logging.path=./logs
```

## Add a Controller that provides RESTFul services

In the project, provide a simple Controller, for example:

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

## Construct the RestTemplate in API model to initiate a call to the RESTful service above

* Construct a RestTemplate synchronous call instance

```java
RestTemplate restTemplate = SofaTracerRestTemplateBuilder.buildRestTemplate();
ResponseEntity<String> responseEntity = restTemplate.getForEntity(
            "http://sac.alipay.net:8080/rest", String.class);
```

* Construct a RestTemplate asynchronous call instance

```java
AsyncRestTemplate asyncRestTemplate = SofaTracerRestTemplateBuilder
    .buildAsyncRestTemplate();
ListenableFuture<ResponseEntity<String>> forEntity = asyncRestTemplate.getForEntity(
            "http://sac.alipay.net:8080/asyncrest", String.class);
```

## Get the RestTemplate in an automatic injection

```java
@Autowired
RestTemplate             restTemplate;
```


## Run the project

Start the SOFABoot app and see the log in the console as follows:

```
2018-10-24 10:45:28.683  INFO 5081 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-10-24 10:45:28.733  INFO 5081 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-10-24 10:45:28.736  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Started RestTemplateDemoApplication in 2.163 seconds (JVM running for 3.603)
```

Successful call：

```
2018-10-24 10:45:28.989  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Response is {"count":1}
2018-10-24 10:45:34.014  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : Async Response is {"count":2}
2018-10-24 10:45:34.014  INFO 5081 --- [           main] c.a.s.t.e.r.RestTemplateDemoApplication  : test finish .......
```

## View log

In the `application.properties`, the log printing directory we configured is `./logs`, which is the root directory of the current application (we can configure it based on actual situation). In the root directory, you can see log files in the structure similar to the followings:

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


In the example, a call to the same RESTful service is initiated by constructing two RestTemplates (one synchronous one asynchronous),After the call is completed, you can see a log similar to the following in the restTemplate-digest.log:

* Digest log

```json
{"time":"2018-10-24 10:45:28.977","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912878910015081","spanId":"0","request.url":"http://sac.alipay.net:8080/rest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":188,"current.thread.name":"main","remote.app":"","baggage":""}
{"time":"2018-10-24 10:45:34.013","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912900410025081","spanId":"0","request.url":"http://sac.alipay.net:8080/asyncrest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":5009,"current.thread.name":"SimpleAsyncTaskExecutor-1","remote.app":"","baggage":""}
```

* Statistical log

```json
{"time":"2018-10-24 10:46:28.769","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/asyncrest"},"count":1,"total.cost.milliseconds":5009,"success":"true","load.test":"F"}
{"time":"2018-10-24 10:46:28.770","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/rest"},"count":1,"total.cost.milliseconds":188,"success":"true","load.test":"F"}
```