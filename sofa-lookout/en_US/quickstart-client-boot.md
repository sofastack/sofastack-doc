# Quick start

This project demonstrates how to use SOFALookout in SOFABoot and connect to the Actuator of Spring Boot. If you want to connect to Prometheus or other Registry, see the Registry section.

## Create a SpringBoot (or SofaBoot) project

Create a new Spring Boot application (In case of SOFABoot project, import to SOFABoot as described in [SOFABoot Documentation - Dependency Management](http://www.sofastack.tech/sofa-boot/docs/DependencyManagement)).

## Introduce Lookout's Starter dependency

Introduce the following dependency in `pom.xml`:

```xml
<dependency>
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-sofa-boot-starter</artifactId>
</dependency>
```

In case of Spring Boot project, it is required to specify a [version](http://mvnrepository.com/artifact/com.alipay.sofa.lookout/lookout-sofa-boot-starter).


## Create a Metrics indicator

After completing the introduction of dependencies, you can add the following methods to the startup class in Spring Boot:

```java
@Autowired
private Registry registry;

@PostConstruct
public void init() {
    Counter counter = registry.counter(registry.createId("http_requests_total").withTag("instant", NetworkUtil.getLocalAddress().getHostName()));
    counter.inc();
}
```

The above code directly injects a Registry field through `@Autowired`. Through the Registry field, you can create the corresponding Counter, and then modify the Counter data to generate the Metrics of the SOFALookout.

## Add configuration item

In SOFABoot project, you need to add a configuration item for the application name: `spring.application.name=xxx`.

## Connect to Spring Boot Actuator

After adding a new indicator, you can choose to connect to the Spring Boot Actuator. Then the following dependency is required:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

After adding the above dependency, you can launch the application locally, visit `http://localhost:8080/metrics`, and you can see the metrics added earlier, as follows:

```
"http_requests_total.instant-MacBook-Pro-4.local": 1,
```

The above codes are at [lookout-client-samples-boot](https://github.com/sofastack/sofa-lookout/tree/master/samples/metrics/client/lookout-client-samples-boot), you can Download them as a reference.
