# Upgrade SOFABoot from 2.3.x/2.4.x to 2.5.x
SOFABoot 2.3.x/2.4.x is developed based on Spring Boot 1.4.2.RELEASE, SOFABoot 2.5.x is developed based on Spring Boot 1.5.x. When upgrading SOFABoot 2.3.x/2.4.x to SOFABoot 2.5.x, we should pay special attention to the differences between the Spring Boot 1.5.x upgrade and the Spring Boot 1.4.x upgrade.

### Renamed Spring Boot Starters
+ spring-boot-starter-ws  -->  spring-boot-starter-web-services
+ spring-boot-starter-redis --> spring-boot-starter-data-redis

### Endpoint Security Control
Spring Boot 1.5.x has security control over all `sensitive endpoints` by default, that is, endpoints such as /beans and /dump, which were previously accessible by default in version 1.4.x, are not accessible in version 1.5.x. To access such endpoints, we need to configure Spring Boot as follows:
> management.security.enabled=false

Only /health, /info, and /docs are accessible by default in version 1.5.x. Please refer to official description for details:
+ [endpoints](https://docs.spring.io/spring-boot/docs/1.5.x-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints)
+ [Accessing sensitive endpoints](https://docs.spring.io/spring-boot/docs/1.5.x-SNAPSHOT/reference/htmlsingle/#production-ready-sensitive-endpoints)

### ApplicationEvent Change
`ApplicationStartedEvent` in 1.4.x has been renamed `ApplicationStartingEvent` in Spring Boot 1.5.x. The version 1.5.x remains forward compatible. Note that the `ApplicationStartedEvent` event has a completely different meaning in version 2.x.

** Users who have upgraded the SOFABoot to the version 2.5.x are strongly advised to change ApplicationStartedEvent to ApplicationStartingEvent to avoid compatibility issues when upgrading SOFABoot to the version 3.0.x in the future.**

### Property renaming
+ server.max-http-post-size  --> server.tomcat.max-http-post-size
+ spring.data.neo4j.session.scope is removed

Refer to the configuration of Spring Boot 1.5.x [changelog](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Configuration-Changelog)

### Summary
The above are the major points worthy of notice when we upgrade SOFABoot 2.3.x/2.4.x to SOFABoot 2.5.x. For detailed information, refer to the [Release Report](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Release-Notes) of Spring Boot 1.5.x.
