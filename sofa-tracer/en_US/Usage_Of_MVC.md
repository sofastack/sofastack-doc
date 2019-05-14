# SpringMVC Integration
In this document will demonstrate how to use SOFATracer to track of SpringMVC, this example [address] (https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-springmvc).

Assuming you have built a simple Spring Web project based on SOFABoot, Then you can be operated by the following steps:

## Introduce dependency

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
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

## Add a simple Controller

In the project code, add a simple Controller, for example:

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

## Run the project

Start Current SOFABoot Application. You will see the log about startup in the console:

```
2018-05-11 11:55:11.932  INFO 66490 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'SpringMvcOpenTracingFilter' to urls: [/*]
2018-05-11 11:55:13.961  INFO 66490 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-05-11 11:55:13.970  INFO 66490 --- [           main] c.a.s.t.e.springmvc.DemoApplication      : Started DemoApplication in 8.361 seconds (JVM running for 9.34)
```

You can access the REST service by visiting [http://localhost:8080/springmvc](http://localhost:8080/springmvc) in your browser. You can see the result similar to the followings:

```json
{
	content: "Hello, SOFATracer SpringMVC DEMO!",
	id: 1,
	success: true
}
```

## View log

In the `application.properties`, the log printing directory we configured is `./logs`, which is the root directory of the current application (we can configure it based on actual situation). In the root directory, you can see log files in the structure similar to the followings:

```
./logs
├── spring.log
└── tracelog
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log
```

Every time you visit [http://localhost:8080/springmvc](http://localhost:8080/springmvc), SOFATracer will log the digest log. You can open the `spring-mvc-digest.log` file to see the specific log content. As for the meaning of each output field, you [can refer to here](https://www.sofastack.tech/sofa-tracer/docs/SpringMVC).

```json
{"time":"2018-05-17 22:20:34.279","local.app":"SOFATracerSpringMVC","traceId":"0a0fe9391526566833985100139443","spanId":"0","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":69,"time.cost.milliseconds":284,"current.thread.name":"http-nio-8080-exec-1","baggage":""}

```