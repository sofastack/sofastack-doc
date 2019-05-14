# Dubbo Integration

In this document will demonstrate how to use SOFATracer to track of Dubbo, this example [address] (https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-dubbo).

## Prepare Environment

The versions of the framework components used in this case are as follows:

* SOFABoot 3.1.1/SpringBoot 2.1.0.RELEASE
* SOFATracer 2.4.0/3.0.4 
* JDK 8

This case includes three submodules:

* tracer-sample-with-dubbo-consumer     service provider
* tracer-sample-with-dubbo-provider     service consumer
* tracer-sample-with-dubbo-facade       service interface define

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

## New tracer-sample-with-dubbo-facade Module

provider a service interface

```java
public interface HelloService {
    String SayHello(String name);
}
```
## New tracer-sample-with-dubbo-provider Module

* provider SOFATracer dependency

    ```xml
    <dependency>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>tracer-sofa-boot-starter</artifactId>
    </dependency>
    ```
    > SOFATracer versions are controlled by SOFABoot versions. If the SOFABoot versions used do not match, you need to manually specify a tracer version that is higher than 2.4.0.

* `application.properties` Configuration

    ```properties
    # Spring boot application
    spring.application.name=dubbo-provider
    # Base packages to scan Dubbo Component: @org.apache.dubbo.config.annotation.Service
    dubbo.scan.base-packages=com.alipay.sofa.tracer.examples.dubbo.impl
    ##  Filter
    dubbo.provider.filter=dubboSofaTracerFilter
    # Dubbo Protocol
    dubbo.protocol.name=dubbo
    ## Dubbo Registry
    dubbo.registry.address=zookeeper://localhost:2181
    logging.path=./logs
    ```
* Publish the Dubbo service using annotations

    ```java
    @Service
    public class HelloServiceImpl implements HelloService {
        @Override
        public String SayHello(String name) {
            return "Hello , "+name;
        }
    }
    ```
 
## New tracer-sample-with-dubbo-consumer Module

* provider SOFATracer dependency

    ```xml
    <dependency>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>tracer-sofa-boot-starter</artifactId>
    </dependency>
    ```

* `application.properties` Configuration

    ```properties
    spring.application.name=dubbo-consumer
    dubbo.registry.address=zookeeper://localhost:2181
    dubbo.consumer.filter=dubboSofaTracerFilter
    logging.path=./logs
    ```
* Service reference

    ```java
    @Reference(async = false)
    public HelloService helloService;
    
    @Bean
    public ApplicationRunner runner() {
        return args -> {
            logger.info(helloService.SayHello("sofa"));
        };
    }
    ```

## Test

Start the tracer-sample-with-dubbo-provider and tracer-sample-with-dubbo-consumer projects; then check the logs:

* dubbo-client-digest.log
```json
{"time":"2019-04-03 11:36:01.909","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"client","local.app":"dubbo-consumer","protocol":"dubbo","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","invoke.type":"sync","remote.host":"10.15.232.69","remote.port":"20880","local.host":"10.15.232.69","client.serialize.time":35,"client.deserialize.time":0,"req.size.bytes":323,"resp.size.bytes":323,"result.code":"00","current.thread.name":"main","time.cost.milliseconds":252,"baggage":""}
```
* dubbo-server-digest.log
```json
{"time":"2019-04-03 11:36:01.880","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"server","local.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","local.host":"10.15.232.69","local.port":"54178","protocol":"dubbo","server.serialize.time":0,"server.deserialize.time":27,"result.code":"00","current.thread.name":"DubboServerHandler-10.15.232.69:20880-thread-2","time.cost.milliseconds":3,"baggage":""}
```

* dubbo-client-stat.log
```json
{"time":"2019-04-03 11:37:01.650","stat.key":{"method":"SayHello","local.app":"dubbo-consumer","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":252,"success":"Y"}
```

* dubbo-server-stat.log
```json
{"time":"2019-04-03 11:37:01.872","stat.key":{"method":"SayHello","remote.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":3,"success":"Y"}
```