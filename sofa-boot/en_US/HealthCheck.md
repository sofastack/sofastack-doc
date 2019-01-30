SOFABoot provides Readiness Check to enhance Spring Boot's Health Check. If you need to use the SOFA middleware, you are advised to use the Health Check extension of SOFABoot to launch application examples in a more elegant way.

## Enable Health Check

To enable the Health Check feature in SOFABoot, you only need to import the following starter:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>healthcheck-sofa-boot-starter</artifactId>
</dependency>
```

**Without the Health Check extension, users still can perform Liveness Check with native Spring Boot Actuator directly relying on the HealthIndicator interface.**


## Security alert

From SOFABoot 2.3.0 on, the Health Check depends on the `Actuator` component in SpringBoot 1.4.x, and the component opens a lot of [EndPoint](https://docs.spring.io/spring-boot/docs/1.4.2. RELEASE/reference/html/production-ready-endpoints.html) such as '/dump ' and '/trace'. So there may be a security risk. Refer to the [Security Recommendations](https://docs.spring.io/spring-boot/docs/1.4.2.RELEASE/reference/html/production-ready-endpoints.html#_security_with_healthindicators) in the official document for settings.

SpringBoot 1.5.x and SpringBoot 2.x. have fixed some security issues. SOFABoot will be supported by upgrading the SpringBoot kernel.

## View Health Check results

After adding the Health Check extension, you can directly browser [http://localhost:8080/health/readiness](http://localhost:8080/health/readiness) to view the Readiness Check results. To view the Liveness Check results, access the URL of the Spring Boot Health Check results. [http://localhost:8080/health](http://localhost:8080/health)。

In SOFABoot, you can also view Health Check results by checking the specific logs in the `health-check` directory. Generally, such logs contain the following content:

```
2018-04-06 23:29:50,240 INFO  main                             - Readiness check result: success
```

At present, the SOFA middleware has controlled upstream traffic access through the Readiness Check offered by SOFABoot. But, apart from the middleware, traffic of an application may come from other sources such as the load balancer. To control such traffic, users are advised to view the Readiness Check results by PAAS and determine whether to launch corresponding nodes in the load balancer based on the results.

** Note: Versions after SOFABoot 2.x no longer indirectly introduce spring-boot-starter-web dependencies. To view Health Check results in the browser, you need to introduce Web container dependencies in the project. **

** Note: In SOFABoot 3.x, the endpoint path has been changed from health/readiness to actuator/readiness**

## Readiness Check extension

SOFABoot allows extension in every phase of the Readiness Check. Applications can be extended according to their needs. In version 2.x, the extendable points are as follows:

Callback interface | Description |
----|-----
org.springframework.context.ApplicationListener | If you want to do something before the Readiness Check, you can monitor the SofaBootBeforeHealthCheckEvent event of this listener.
org.springframework.boot.actuate.health.HealthIndicator | If you want to add a check item to the Readiness Check in SOFABoot, you can directly extend this interface of Spring Boot.
com.alipay.sofa.healthcheck.startup.SofaBootAfterReadinessCheckCallback | If you want to do something after the Readiness Check, you can extend this interface of SOFABoot.

In version 3.x, extendable points are as follows:

Callback interface | Description |
----|-----
com.alipay.sofa.healthcheck.core.HealthChecker | if you want to add a check item to the Readiness Check in SOFABoot, you can extend the interface directly. Compared with the HealthIndicator interface of Spring Boot, this interface provides additional parameters, for example, check the number of retries.
org.springframework.boot.actuate.health.HealthIndicator | If you want to add a check item to the Readiness Check in SOFABoot, you can directly extend this interface of Spring Boot.
org.springframework.boot.actuate.health.ReactiveHealthIndicator | If you want to add a check item to SOFABoot's Readiness Check in WebFlux, you can directly extend this interface of Spring Boot.
com.alipay.sofa.healthcheck.startup.ReadinessCheckCallback | if you want to do something after the Readiness Check, you can extend this interface of SOFABoot.

Note that you can set the execution order of the above four extension interfaces through Spring Boot's standard `Ordered` and `PriorityOrdered` interfaces and `@order` annotation.

## Configuration of Readiness Check

After enabled the Health Check extension of SOFABoot to an application, you can add configuration items in Spring Boot's profile `application.properties` to customize the Readiness Check behavior.

Readiness Check configuration item | Instructions | Default | Supported since version |
----|------|------|----
com.alipay.sofa.healthcheck.skip.all | Whether to skip the entire Readiness Check | false | 2.4.0
com.alipay.sofa.healthcheck.skip.component | Whether to skip the SOFA middleware Readiness Check | false | 2.4.0
com.alipay.sofa.healthcheck.skip.indicator | Whether to skip the HealthIndicator Readiness Check | false | 2.4.0
com.alipay.sofa.healthcheck.component.check.retry.count | The number of component Health Check retries | 20 | 2.4.10 (the value is 0 in previous versions)
com.alipay.sofa.healthcheck.component.check.retry.interval | Interval between component Health Check retries | 1,000 (ms) | 2.4.10 (the value is 0 in previous versions)
com.alipay.sofa.healthcheck.module.check.retry.count | the number of sofaboot module Health Check retries | 0 | 2.4.10
com.alipay.sofa.healthcheck.module.check.retry.interval | Interval between sofaboot module Health Check retries | 1,000 (ms) | 2.4.10 (the value is 0 in previous versions)

