# Record HttpClient call data with SOFATracer

This document demonstrates how to log link data in a file by configuring how HttpClient is used in an application that integrates SOFATracer.

## Prepare environment

To use SOFABoot, you need to prepare the basic environment first. SOFABoot relies on the following environments:
- JDK7 or JDK8
- Apache Maven 3.2.5+ required for compilation

## Introduce SOFATracer

After creating a Spring Boot project, you need to introduce the SOFABoot dependency. First, you need to unzip the `zip` package of the Spring Boot project generated above and modify the Maven project configuration file `pom.xml`.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/>
</parent>
```

Replace the above with the followings:

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofa.boot.version}</version>
</parent>
```
The `${sofa.boot.version}` specifies the latest version of SOFABoot. For more about SOFABoot versions, see [Release notes](https://github.com/alipay/sofa-boot/releases). 

Then, add the SOFATracer dependency to the project:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
```

Finally, add the parameters to be used by SOFATracer under the project's `application.properties` file, including `spring.application.name` that indicates the current application name and `logging.path` that specifies the log output directory.

```properties
# Application Name
spring.application.name=HttpClientDemo
# logging path
logging.path=./logs
```

## Add HttpClient, SOFATracer dependencies and SOFATracer HttpClient plugin

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-tracer-httpclient-plugin</artifactId>
    <version>2.2.0</version>
</dependency>
<!-- HttpClient Dependency -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <!-- Version 4.5.X Series -->
    <version>4.5.3</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpasyncclient</artifactId>
    <!-- Version 4.X Series -->
    <version>4.1.3</version>
</dependency>
```


## Add a Controller that provides RESTful service

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


## Construct HttpClient to initiate a call to the RESTful service above

For the detailed process of constructing HttpClient, see the `HttpClientInstance` (synchronous) and `HttpAsyncClientInstance` (asynchronous) constructing methods in this document.

To enable the third-party open-source component HttpClient to support the link call of SOFATracer, SOFATracer provides the extension plugin of HttpClient, namely sofa-tracer-httpclient-plugin. For accurate event tracking using SOFATracer HttpCliet, you need to use HttpClientBuilder to construct an instance of HttpClient and need to explicitly call SofaTracerHttpClientBuilder.clientBuilder (httpClientBuilder) to construct a HttpClientBuilder which has been tracked by SOFATracer. The key code sample is as follows:

* Construct an HttpClient synchronous call instance:

```java
HttpClientBuilder httpClientBuilder = HttpClientBuilder.create();
// SOFATracer
SofaTracerHttpClientBuilder.clientBuilder(httpClientBuilder);
CloseableHttpClient httpClient = httpClientBuilder.setConnectionManager(connManager).disableAutomaticRetries()
                .build();
```

* Construct an HttpClient asynchronous call instance:

```java
RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(6000).setConnectTimeout(6000).setConnectionRequestTimeout(6000).build();
HttpAsyncClientBuilder httpAsyncClientBuilder = HttpAsyncClientBuilder.create();
//tracer
SofaTracerHttpClientBuilder.asyncClientBuilder(httpAsyncClientBuilder);
CloseableHttpAsyncClient asyncHttpclient = httpAsyncClientBuilder.setDefaultRequestConfig(requestConfig).build();
```

When you construct the HttpClient via the SofaTracerHttpClientBuilder (clientBuilder method for synchronous call instance, and asyncClientBuilder method for asynchronous call instance) to initiate a call to the RESTful service above, the link data of SOFATracer can be tracked.

## Run the project

You can import the project into IDE and run the `main` method in the project (in this example, the main method is in HttpClientDemoApplication) to start the application. In the console, you can see the log about startup as follows:

```json
2018-09-27 20:31:21.465  INFO 33277 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-09-27 20:31:21.599  INFO 33277 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-09-27 20:31:21.608  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Started HttpClientDemoApplication in 5.949 seconds (JVM running for 6.573)
```

If there is a log similar to the followings, it indicates that the call to HttpClient succeeded:

```json
2018-09-27 20:31:22.336  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Response is {"count":1,"name":"httpclient"}
2018-09-27 20:31:22.453  INFO 33277 --- [           main] c.a.s.t.e.h.HttpClientDemoApplication    : Async Response is {"count":2,"name":"httpclient"}
```


## View log

In the `application.properties`, the log printing directory we configured is `./logs`, which is the root directory of the current application (we can configure it based on actual situation). Thus, in the root directory of the current project, you can see log files in the structure similar to the followings:

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

In the example, a call to the same RESTful service is initiated by constructing two HttpClients (one  is synchronous and the other is asynchronous). After the call is completed, you can see the log similar to the followings in httpclient-digest.log. About the meaning of each output field, you can see documentation for SOFATracer.

```json
{"time":"2018-09-27 20:31:22.328","local.app":"HttpClientDemo","traceId":"0a0fe8801538051482054100133277","spanId":"0","request.url":"http://localhost:8080/httpclient","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":-1,"time.cost.milliseconds":274,"current.thread.name":"main","remote.app":"","baggage":""}
{"time":"2018-09-27 20:31:22.443","local.app":"HttpClientDemo","traceId":"0a0fe8801538051482410100233277","spanId":"0","request.url":"http://localhost:8080/httpclient","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":-1,"time.cost.milliseconds":33,"current.thread.name":"I/O dispatcher 1","remote.app":"","baggage":""}
```

Attachment: [Source code](https://github.com/alipay/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-httpclient) for this sample project.

