## SOFABoot 介绍

SOFABoot 是蚂蚁金服开源的基于 Spring Boot 的研发框架，它在 Spring Boot 的基础上，提供了诸如 Readiness Check，类隔离，日志空间隔离等等能力。在增强了 Spring Boot 的同时，SOFABoot 提供了让用户可以在 Spring Boot 中非常方便地使用 SOFA 中间件的能力。

当前 SOFABoot 的 2.3.2 版本是基于 Spring Boot 1.4.2.RELEASE 来构建的。

## 功能描述

SOFABoot 在 Spring Boot 的基础上，提供了以下能力：

* 扩展 Spring Boot 的健康检查的能力：在 Spring Boot 健康检查能力的基础上，提供了 Readiness Check 的能力，保证应用实例安全上线。
* 日志空间隔离能力：中间件框架自动发现应用的日志实现依赖并独立打印日志，避免中间件和应用日志实现绑定，通过 [sofa-common-tools](https://github.com/alipay/sofa-common-tools) 实现。
* 提供类隔离的能力：基于 [SOFAArk](https://github.com/alipay/sofa-ark) 框架提供类隔离能力，方便使用者解决各种类冲突问题。
* 中间件的集成管理：统一管控、提供中间件统一易用的编程接口、每一个 SOFA 中间件都是独立可插拔的组件
* 完全兼容 Spring Boot：SOFABoot 基于 Spring Boot 的基础上进行构建，并且完全兼容 Spring Boot。

## 应用场景

SOFABoot 本身就脱胎于蚂蚁金服内部对于 Spring Boot 的实践，补充了 Spring Boot 在大规模金融级的生产场景下的一些不足的地方，所以 SOFABoot 也特别适合于这样的场景。

当然，SOFABoot 的每个组件都是可选的，用户可以灵活选择其中的功能来使用，比如如果仅仅想在 Spring Boot 下面引入 SOFA 中间件，就不需要引入 SOFABoot 中的类隔离的能力。