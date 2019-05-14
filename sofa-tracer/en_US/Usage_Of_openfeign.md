# OpenFeign Integration

In this document will demonstrate how to use SOFATracer to track of OpenFeign, this example [address] (https://github.com/sofastack/sofa-tracer/tree/3.x/tracer-samples/tracer-sample-with-openfeign)。

## Prepare Environment

The versions of the framework components used in this case are as follows:

* Spring Cloud Greenwich.RELEASE
* SOFABoot 3.1.1/SpringBoot 2.1.0.RELEASE
* SOFATracer 3.0.4 
* JDK 8

This case includes two submodules:

* tracer-sample-with-openfeign-provider  service provider
* tracer-sample-with-openfeign-consumer  service consumer

## New SOFABoot project as parent project

After creating a Spring Boot project, you need to introduce the SOFABoot's dependency. First, you need to unzip the generated `zip` package of Spring Boot project and modify the Maven project configuration file `pom.xml`.

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
The `${sofa.boot.version}` specifies the latest version of SOFABoot. For more information about SOFABoot versions, refer to [Release notes](https://github.com/sofastack/sofa-boot/releases).

## New tracer-sample-with-openfeign-provider Module

* Introducing dependence 

    ```xml
    <dependency>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>tracer-sofa-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    ```
    > SOFATracer versions are controlled by SOFABoot versions. If the SOFABoot versions used do not match, you need to manually specify a tracer version that is higher than 3.0.4.

* `application.properties` Configuration

    ```properties
    spring.application.name=tracer-provider
    server.port=8800
    spring.cloud.zookeeper.connect-string=localhost:2181
    spring.cloud.zookeeper.discovery.enabled=true
    spring.cloud.zookeeper.discovery.instance-id=tracer-provider
    ```
* Simple resource class

    ```java
    @RestController
    public class UserController {
        @RequestMapping("/feign")
        public String testFeign(HttpServletRequest request) {
            return "hello tracer feign";
        }
    }
    ```
## New tracer-sample-with-openfeign-consumer Module

* Introducing dependence 

    ```xml
    <dependency>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>tracer-sofa-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    ```

* `application.properties` Configuration

    ```properties
    spring.application.name=tracer-consumer
    server.port=8082
    spring.cloud.zookeeper.connect-string=localhost:2181
    spring.cloud.zookeeper.discovery.enabled=true
    spring.cloud.zookeeper.discovery.instance-id=tracer-consumer
    ```
* Define the feign resource

    ```java
    @FeignClient(value = "tracer-provider",fallback = FeignServiceFallbackFactory.class)
    public interface FeignService {
        @RequestMapping(value = "/feign", method = RequestMethod.GET)
        String testFeign();
    }
    ```
* User @EnableFeignClients And @EnableDiscoveryClient

    ```java
    @SpringBootApplication
    @RestController
    @EnableDiscoveryClient
    @EnableFeignClients
    public class FeignClientApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(FeignClientApplication.class,args);
        }
    
        @Autowired
        private FeignService feignService;
    
        @RequestMapping
        public String test(){
            return feignService.testFeign();
        }
    }
    ```

## Test

Start tracer-sample-with-openfeign-provider and tracer-sample-with-openfeign-consumer two projects; then browser access:Http://localhost:8082/ . Then check the log:

In the `application.properties`, the log printing directory we configured is `./logs`, which is the root directory of the current application (we can configure it based on actual situation). In the root directory, you can see log files in the structure similar to the followings:

```
./logs
├── spring.log
└── tracelog
    ├── feign-digest.log
    ├── feign-stat.log
    ├── spring-mvc-digest.log
    ├── spring-mvc-stat.log
    ├── static-info.log
    └── tracer-self.log
```

In the example, you can see logs like the following in feign-digest.log

```json
{"time":"2019-03-28 18:08:06.800","local.app":"tracer-consumer","traceId":"0a0fe88f1553767685981100124403","spanId":"0.1","request.url":"http://10.15.232.143:8800/feign","method":"GET","result.code":"200","error":"","req.size.bytes":0,"resp.size.bytes":18,"time.cost.milliseconds":206,"current.thread.name":"http-nio-8082-exec-1","remote.host":"10.15.232.143","remote.port":"","component.client.impl":"open-feign","baggage":""}
```