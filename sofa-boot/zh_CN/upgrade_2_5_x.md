# SOFABoot 2.3.x/2.4.x 升级到 2.5.x
SOFABoot 2.3.x/2.4.x 基于 Spring Boot 1.4.2.RELEASE 版本开发，SOFABoot 2.5.x 则是基于 Spring Boot 1.5.x 版本开发。 从 SOFABoot 2.3.x/2.4.x 升级到 SOFABoot 2.5.x 需要重点考虑 Spring Boot 1.5.x 相较 Spring Boot 1.4.x 的升级注意点。

### 重命名的 spring boot starters
+ spring-boot-starter-ws  -->  spring-boot-starter-web-services
+ spring-boot-starter-redis --> spring-boot-starter-data-redis

### endpoint 安全性控制
Spring Boot 1.5.x 对所有 `Sensitive Endpoint` 默认进行了安全管控，即之前在 1.4.x 默认能访问的诸如 /beans, /dump 等 endpoints 在 1.5.x 版本均不能访问。如果需要访问，需要配置：
> management.security.enabled=false

默认情况下，在 1.5.x 只有 /health, /info, /docs 能够访问。详细请参考官方描述：
+ [endpoints](https://docs.spring.io/spring-boot/docs/1.5.x-SNAPSHOT/reference/htmlsingle/#production-ready-endpoints)
+ [Accessing sensitive endpoints](https://docs.spring.io/spring-boot/docs/1.5.x-SNAPSHOT/reference/htmlsingle/#production-ready-sensitive-endpoints)

### ApplicationEvent 变更
Spring Boot 1.5.x 将 1.4.x 中的 `ApplicationStartedEvent` 重命名为 `ApplicationStartingEvent`，在 1.5.x 仍然保持向前兼容。需要格外注意的是，在 2.x 版本中，`ApplicationStartedEvent` 事件意义完全不一样。

**强烈建议升级到 SOFABoot 2.5.x 的用户，将应用中使用的 ApplicationStartedEvent 变更为 ApplicationStartingEvent；避免今后升级至 SOFABoot 3.0.x 出现兼容性问题**

### Property 重命名
+ server.max-http-post-size  --> server.tomcat.max-http-post-size
+ spring.data.neo4j.session.scope 被移除

具体参考 Spring Boot 1.5.x 配置的 [changelog](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Configuration-Changelog)

### 总结
以上总结了从 SOFABoot 2.3.x/2.4.x 升级到 SOFABoot 2.5.x 的几个主要注意点，详细可以参考 Spring Boot 1.5.x 的[发布报告](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Release-Notes)