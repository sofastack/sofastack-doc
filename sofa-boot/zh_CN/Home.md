## SOFABoot 介绍

SOFABoot 是蚂蚁金服开源的基于 Spring Boot 的研发框架，它在 Spring Boot 的基础上，提供了诸如 Readiness Check，类隔离，日志空间隔离等能力。在增强了 Spring Boot 的同时，SOFABoot 提供了让用户可以在 Spring Boot 中非常方便地使用 SOFA 中间件的能力。

你可以在[发布历史](https://github.com/sofastack/sofa-boot/releases)中查看所有的发布报告，SOFABoot 版本和 Spring Boot 版本对应关系如下：

|SOFABoot 版本| Spring Boot 版本|
|:---:|:---:|
|2.3.x|1.4.2.RELEASE|
|2.4.x|1.4.2.RELEASE|
|2.5.x|1.5.16.RELEASE|
|3.0.x|2.0.3.RELEASE|
|3.1.x|2.1.0.RELEASE|

即 SOFABoot 2.3.x 和 2.4.x 系列版本构建在 Spring Boot 1.4.2.RELEASE 基础之上；SOFABoot 2.5.x 系列版本构建在 Spring Boot 1.5.x 基础之上；SOFABoot 3.x 系列版本将构建在 Spring Boot 2.x 基础之上。你可以在[发布历史](https://github.com/sofastack/sofa-boot/releases)中查看获取所有的历史版本代码。另外为了方便社区同学能够基于最新开发版本的 SOFABoot 进行开发学习，我们会发布当前开发分支的 SNAPSHOT 版本。为顺利从中央仓库拉取 SNAPSHOT 包，需要在本地 maven setting.xml 文件增加如下 profile 配置:

```xml
<profile>
    <id>default</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <repositories>
        <repository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </pluginRepository>
    </pluginRepositories>
</profile>
```

目前 SOFABoot 最新版本为 3.1.0，基于 Spring Boot 2.1.0.RELEASE, 支持 JDK11。

## 功能描述

SOFABoot 在 Spring Boot 基础上，提供了以下能力：

* 扩展 Spring Boot 健康检查的能力：在 Spring Boot 健康检查能力基础上，提供了 Readiness Check 的能力，保证应用实例安全上线。
* 提供模块化开发的能力：基于 Spring 上下文隔离提供[模块化开发](./Modular-Development)能力，每个 SOFABoot 模块使用独立的 Spring 上下文，避免不同 SOFABoot 模块间的 BeanId 冲突。
* 增加模块并行加载和 Spring Bean 异步初始化能力，加速应用启动；
* 增加日志空间隔离的能力：中间件框架自动发现应用的日志实现依赖并独立打印日志，避免中间件和应用日志实现绑定，通过 [sofa-common-tools](https://github.com/sofastack/sofa-common-tools) 实现。
* 增加类隔离的能力：基于 [SOFAArk](https://github.com/sofastack/sofa-ark) 框架提供类隔离能力，方便使用者解决各种类冲突问题。
* 增加中间件集成管理的能力：统一管控、提供中间件统一易用的编程接口、每一个 SOFA 中间件都是独立可插拔的组件。
* 提供完全兼容 Spring Boot的能力：SOFABoot 基于 Spring Boot 的基础上进行构建，并且完全兼容 Spring Boot。

## 应用场景

SOFABoot 本身就脱胎于蚂蚁金服内部对于 Spring Boot 的实践，补充了 Spring Boot 在大规模金融级生产场景下一些不足的地方，所以 SOFABoot 特别适合于这样的场景。

当然，SOFABoot 的每个组件都是可选的，用户可以灵活选择其中的功能来使用，比如如果仅仅想在 Spring Boot 下面引入 SOFA 中间件，可以不需引入 SOFABoot 中的类隔离能力。
