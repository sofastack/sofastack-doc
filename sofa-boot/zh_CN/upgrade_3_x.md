## 前言 
SOFABoot 是蚂蚁金服开源的基于 Spring Boot 的研发框架，它在 Spring Boot 的基础上，提供了诸如 Readiness Check，类隔离，日志空间隔离等能力。在增强了 Spring Boot 的同时，SOFABoot 提供了让用户可以在 Spring Boot 中非常方便地使用 SOFA 中间件的能力。

自今年 4 月份 SOFABoot 开源至今，我们收到了非常多来自社区同学的反馈，也非常高兴的看到很多社区同学积极的参与到 SOFAStack 开源共建，这极大了鼓舞了我们建设 SOFAStack 开源社区的决心，力图把 SOFAStack 社区和生态建设更加繁荣。在此，我们宣布推出 SOFABoot 3.0 版本，SOFABoot 3.0 是基于 Spring Boot 2.0 版本开发。在 SOFABoot 3.0 中，可以将 SOFABoot 扩展能力和 Spring Boot 2.x 官方组件无缝集成。此外，我们在 SOFABoot 3.0 中兼容了 Spring Cloud 组件集成，可以很方便地在 SOFABoot 框架中集成 Spring Cloud 组件，如 Zuul, Config 等。

以下，本文将详细介绍 SOFABoot 3.0 相较 SOFABoot 2.x 的变更。

## Spring Boot 升级 2.x
SOFABoot 3.0 版本升级 Spring Boot 版本至 2.0。鉴于Spring Boot 社区最近刚公告 1.x 版本将维护至明年 8 月份为止，未来，我们也将主力维护 SOFABoot 3.x 版本，近期也将发布 SOFABoot 3.1 升级 Spring Boot 版本至 2.1。

## Spring Cloud 兼容
在 SOFABoot 2.x 中，存在部分组件和 SOFABoot 兼容性问题。在 SOFABoot 3.x 中，对 Spring Cloud 各组件进行了比较完备的兼容性测试和问题修复，保证了 SOFABoot 3.x 与 Spring Cloud 良好的兼容性。

## WebFlux 框架兼容
Spring Boot 2.x 引入了 WebFlux 框架，SOFABoot 3.x 主要在两个方面兼容了 WebFlux 框架；
+ 健康检查兼容了 ReactiveHealthIndicator 扩展接口，业务对这个接口的扩展实现将会纳入到 Readiness Check；
+ 兼容对 WebFlux 网络请求进行埋点，埋点日志格式和文件保持对普通 MVC 请求兼容，详细参考[MVC 埋点请求](https://www.sofastack.tech/sofa-tracer/docs/Usage_Of_MVC)

## JDK 版本支持
SOFABoot 3.x 最低要求运行在 JDK 8 及其以上版本，不支持 JDK 6，7。

## 健康检查
SOFABoot 为 Spring Boot 的健康检查能力增加了 Readiness Check 能力，以确保应用在正常对外服务前，所有组件及业务本身处于健康状态。相较于与 SOFABoot 2.x, SOFABoot 3.0 在健康检查做了很大的重构，主要是剥离了部分蚂蚁金服内部兼容逻辑，采用更加友好的编码方案；其次，SOFABoot 3.0 健康检查提供了多种不同场景下的健康检查扩展形式，支持 Spring Boot 2.x 引入的 `ReactiveHealthIndicator` 扩展接口，丰富了健康检查扩展特性。

### 调整 Readiness Check Endpoint 路径
在 SOFABoot 2.x 中，查看健康检查结果的 Endpoint 为 `/health/readiness`，而在 SOFABoot 3.0 中，变更为 `/actuator/readiness`。

### 扩展接口变更
在 SOFABoot 3.x 中，提供四种方式扩展健康检查，分别是
+ HealthChecker
+ HealthIndicator(Spring Boot 原生)
+ ReactiveHealthIndicator(Spring Boot 原生)
+ ReadinessCheckCallback

这四个接口的扩展实现执行顺序是 HealthChecker > HealthIndicator, ReactiveHealthIndicator > ReadinessCheckCallback，相同接口的扩展实现执行顺序则遵循 Spring Boot 标准的方案。即扩展类可以额外实现两个标准的 Order 接口：

+ org.springframework.core.Ordered
+ org.springframework.core.PriorityOrdered

或者使用注解

+ org.springframework.core.annotation.Order

这些接口的扩展实现处理结果将会在健康检查结果查中展现。

### 删除 SofaBootBeforeHealthCheckEvent 事件
在 SOFABoot 2.x 中，我们没有提供支持对健康检查扩展实现进行排序，导致用户无法预期自身扩展执行时机。如上述，SOFABoot 3.x 支持各组件扩展实现的排序，因此该事件可以统一使用 `HealthChecker` 接口和高优先级顺序实现替代。其次，在 SOFABoot 2.x 中，`SofaBootBeforeHealthCheckEvent ` 事件的处理逻辑结果并不会反应在健康检查结果中，使用 `HealthChecker` 替代之后，这部分逻辑处理自然变成健康检查的一部分，可供查看。

### 删除 DefaultHealthChecker 接口
使用 JDK8 默认方法特性，删除 DefaultHealthChecker 接口，用户可以直接使用 HealthChecker 接口替代 DefaultHealthChecker.

### 删除 SofaBootMiddlewareAfterReadinessCheckCallback 和 SofaBootAfterReadinessCheckCallback 接口
在 SOFABoot 2.x 中，这两个接口是两种场景下的健康检查回调；推荐 SOFABoot 官方 Starter 使用 SofaBootMiddlewareAfterReadinessCheckCallback，而业务应用推荐使用SofaBootAfterReadinessCheckCallback，框架将优先执行 SofaBootMiddlewareAfterReadinessCheckCallback 的扩展实现，然后执行 SofaBootAfterReadinessCheckCallback 的扩展实现。这样的设计有两个缺陷：
+ 两个接口本质没有区别，但是隐藏了先后顺序逻辑，给用户引入了额外的学习成本；
+ 只考虑了 SofaBootMiddlewareAfterReadinessCheckCallback 和 SofaBootAfterReadinessCheckCallback 两个接口的顺序，但无法保证相同接口实现的执行顺序。

SOFABoot 3.x 中则引入了统一的健康检查回调接口 ReadinessCheckCallback，无论是官方 Starter 还是业务都实现这个接口，顺序的控制则遵循 Spring Boot 标准的方案。即通过额外实现两个标准的 Order 接口：

+ org.springframework.core.Ordered
+ org.springframework.core.PriorityOrdered

或者使用注解

+ org.springframework.core.annotation.Order

控制各个扩展实现的执行顺序。
## 版本查看
### 调整 Versions Endpoint 路径
在 SOFABoot 2.x 中，版本信息查看的 Endpoint 为 /sofaboot/versions ，而在 SOFABoot 3.0 中，变更为 /actuator/versions

## 总结
以上是 SOFABoot 3.x 详细的变更记录，近期有升级或者试用的同学可以通过 [Github](https://github.com/sofastack/sofa-boot) 联系，我们会及时的答复与支持。
