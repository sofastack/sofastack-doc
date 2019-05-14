## Preface 
As a Spring Boot-based development framework open sourced by Ant Financial, SOFABoot provides capabilities such as Readiness Check, class isolation, and log space isolation. In addition to enhancing the Spring Boot, SOFABoot provides users with the capability to easily use SOFA middleware in Spring Boot.

We have received a lot of feedback from community users since SOFABoot was open sourced in April 2018. We are also very pleased to see many community users take an active part in building the SOFAStack open source, which greatly increases our determination to prosper SOFAStack community and ecosystem. Here, we announce the release of the SOFABoot 3.0, which is developed based on Spring Boot 2.0. SOFABoot 3.0 allows us to seamlessly integrate the extension capability of SOFABoot with official components of Spring Boot 2.x. In addition, SOFABoot 3.0 is compatible with Spring Cloud components, which allows us to easily integrate Spring Cloud components like Zuul and Config in the SOFABoot framework.

Below are the major changes of SOFABoot 3.0 compared with SOFABoot 2.x.

## Upgrade Spring Boot to version 2.x
Upgrade Spring Boot in SOFABoot 3.0 to version 2.0. As the Spring Boot community recently announced that the maintenance for version 1.x will end in August 2019, we will focus on SOFABoot 3.x in the future and will release SOFABoot 3.1 with Spring Boot upgraded to version 2.1 soon.

## Spring Cloud compatible
Some components are not compatible with SOFABoot in SOFABoot 2.x. In SOFABoot 3.x, we have run thorough compatibility tests on Spring Cloud components and fixed all problems found to ensure good compatibility between SOFABoot 3.x and Spring Cloud.

# WebFlux framework compatible
Spring Boot 2.x introduces the WebFlux framework. SOFABoot 3.x is compatible with WebFlux in two major aspects;
+ Health Check is compatible with the ReactiveHealthIndicator extension interface. The Readiness Check will include the implementation of the interface extension;
+ Compatible with buried points of WebFlux web requests. Point burying logs and files are compatible with common MVC requests. For detailed information, refer to [MVC point burying request](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_MVC).

### JDK version support
SOFABoot 3.x must run on JDK 8 or higher versions and does not support JDK 6 and JDK 7.

## Health Check
SOFABoot adds Readiness Check capability to Spring Boot's Health Check capability, to ensure that all components and operations are in a healthy state before the application goes into services. Compared with SOFABoot 2.x, SOFABoot 3.0 features great adjustments to the Health Check. It abandons some internal compatibility logic of Ant Financial and uses a friendlier coding scheme. Besides, the Health Check of SOFABoot 3.0 offers extensions in various scenarios, supports the 'ReactiveHealthIndicator' extension interface introduced in Spring Boot 2.x, and provides more Health Check extension features.

### Adjust the Readiness Check Endpoint path
The Endpoint for checking the Health Check result is '/health/readiness' in SOFABoot 2.x. However, it has been changed to '/actuator/readiness' in SOFABoot 3.0.

### Extension interface change
SOFABoot 3.x provides the following four ways to expand health checks:
+ HealthChecker
+ HealthIndicator (native Spring Boot)
+ ReactiveHealthIndicator (native Spring Boot)
+ ReadinessCheckCallback

Extension of these four interfaces is executed in the order of HealthChecker > HealthIndicator, ReactiveHealthIndicator > ReadinessCheckCallback. Extensions through the same interface are executed in the order specified in the Spring Boot standard scheme. That is to say, the extension class can implement two additional standard Order interfaces:

+ org.springframework.core.Ordered
+ org.springframework.core.PriorityOrdered

or use annotations

+ org.springframework.core.annotation.Order

The results of the extension implementation of these interfaces will be presented in the Health Check results.

### Deleting the SofaBootBeforeHealthCheckEvent
SOFABoot 2.x does not support ordering the Health Check extension implementations. As a result, users are unable to expect the time when their extension will be implemented. As mentioned above, SOFABoot 3.x supports ordering component extension implementations. Extension of the unified 'HealthChecker' interface has the highest priority, so the event is no longer necessary. In addition, the logical outcome of 'SofaBootBeforeHealthCheckEvent' event processing is not shown in the Health Check result in SOFABoot 2.x. After 'HealthChecker' is used, this logical processing becomes a part of the Health Check and it available for viewing.

### Deleting the DefaultHealthChecker Interface
The DefaultHealthChecker interface is deleted by the default method in JDK8. Users can use the HealthChecker interface as an alternative.

### Deleting SofaBootMiddlewareAfterReadinessCheckCallback and SofaBootAfterReadinessCheckCallback Interfaces
The two interfaces are the Health Check callbacks for two scenarios in SOFABoot 2.x. You are recommended to use SofaBootMiddlewareAfterReadinessCheckCallback for the official SOFABoot Starter and SofaBootAfterReadinessCheckCallback for service applications because the framework will first implement the extension of the former callback. There are two flaws in this design:
+ The two interfaces are the same in essence. The implicit ordering logic brings an additional learning cost for users.
+ The design only considers the order of the two interfaces and does not specify the order of implementations of the same interface.

SOFABoot 3.x introduces ReadinessCheckCallback, a unified Health Check callback interface that has been implemented by both the official Starter and service applications. The order of implementations follows the Spring Boot standard scheme. That is to say, two additional standard Order interfaces have been implemented:

+ org.springframework.core.Ordered
+ org.springframework.core.PriorityOrdered

or use annotations

+ org.springframework.core.annotation.Order

to control the execution order of extension implementations.
## Viewing Version Information
### Adjusting Versions Endpoint Path
The Endpoint for viewing version information is /sofaboot/versions in SOFABoot 2. However, it has been changed to /actuator/versions in SOFABoot 3.0.

## Conclusion
The above are the detailed change records for SOFABoot 3.x. If you want an upgrade or a trial version, please contact us through [Github](https://github.com/sofastack/sofa-boot). We will reply promptly and provide support.

