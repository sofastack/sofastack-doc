title: 产品概述
---
## 产品描述
SOFAArk 是一款基于 Java 实现的轻量级类隔离加载容器，主要是为应用程序提供类隔离和依赖包隔离的能力，由蚂蚁金服公司开源贡献；基于 [Fat Jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html#executable-jar-jar-file-structure) 技术，简单的单模块 Java 应用或者 Spring Boot 应用均可被打包成一个自包含可运行的 Fat Jar，称之为 Ark 包；Ark 包内嵌 SOFAArk 类隔离容器，通过 `java -jar` 命令启动 Ark 包，SOFAArk 容器将优先启动，进而由容器负责启动各 Ark 插件和应用；

## 背景
日常使用 Java 开发，常常会遇到包依赖冲突的问题，尤其当应用变得臃肿庞大，包冲突的问题也会变得更加棘手，导致各种各样的报错，例如 `LinkageError`, `NoSuchMethodError` 等；实际开发中，可以采用多种方法来解决包冲突问题，比较常见的是类似 Spring Boot 的做法，统一管理应用所有依赖包的版本，保证这些三方包不存在依赖冲突；这种做法只能有效避免包冲突问题，不能根本上解决包冲突的问题；如果某个应用的确需要在运行时使用两个相互冲突的包，例如 `protobuf2` 和 `protobuf3`，那么类似 Spring Boot 的做法依然解决不了问题。

为了彻底解决包冲突的问题，需要借助类隔离机制，使用不同的 `ClassLoader` 加载不同版本的三方依赖，进而隔离包冲突问题； `OSGI` 作为业内最出名的类隔离框架，自然是可以被用于解决上述包冲突问题，但是 `OSGI` 框架太过臃肿，功能繁杂；为了解决包冲突问题，引入 `OSGI` 框架，有牛刀杀鸡之嫌，且反而使工程变得更加复杂，不利于开发；

SOFAArk 专注于解决类隔离问题，采用轻量级的类隔离方案来解决日常经常遇到的包冲突问题，在蚂蚁金服内部服务于整个 [SOFABoot](https://github.com/alipay/sofa-boot) 技术体系，弥补 Spring Boot 没有的类隔离能力。实际上，SOFAArk 是一个通用的轻量级类隔离框架，并不限于 Spring Boot 应用，也可以和其他的 Java 开发框架集成； 

## 原理
SOFAArk 包含三个概念，`Ark Container`, `Ark Plugin` 和 `Ark Biz`; 运行时逻辑结构图如下:

![image.png | center | 1310x1178](https://cdn.yuque.com/lark/2018/png/590/1523868989241-f50695ed-dca0-4bf7-a6a9-afe07c2ade76.png)

在介绍这三个概念之前，先介绍上述 `Ark` 包概念；`Ark` 包是满足特定目录格式要求的可运行 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成标准格式的 `Ark` 包；使用 `java -jar` 命令即可在 SOFAArk 容器之上启动应用；`Ark` 包通常包含 `Ark Container`、`Ark Plugin` 和 `Ark Biz`；以下我们针对这三个概念简单做下名词解释：

+ `Ark Container`: SOFAArk 容器，负责 `Ark` 包启动运行时的管理；`Ark Plugin` 和 `Ark Biz` 运行在 SOFAArk 容器之上；容器具备管理插件和应用的功能；容器启动成功后，会自动解析 classpath 包含的 `Ark Plugin` 和 `Ark Biz` 依赖，完成隔离加载并按优先级依次启动之；

+ `Ark Plugin`: Ark 插件，满足特定目录格式要求的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-plugin-maven-plugin` 可以将一个或多个普通的 `Java jar` 打包成一个标准格式的 `Ark Plugin`；`Ark Plugin` 会包含一份配置文件，通常包括插件类导入导出配置、资源导入导出配置、插件启动优先级等；运行时，SOFAArk 容器会使用独立的 `PluginClassLoader`加载插件，并根据插件配置构建类加载索引表、资源加载索引表，使插件和插件之间、插件和应用之间相互隔离；

+ `Ark Biz`: Ark 应用模块，满足特定目录格式要求的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成一个标准格式的 `Ark Biz`；`Ark Biz` 是工程应用以及其依赖包的组织单元，包含应用启动所需的所有依赖和配置；

运行 `Ark` 包，`Ark Container` 优先启动，容器自动解析 `Ark` 包中含有的 `Ark Plugin` 和 `Ark Biz`，并读取他们的配置信息，构建类和资源的加载索引表；然后使用独立的 `ClassLoader` 加载并按优先级配置依次启动；需要指出的是，`Ark Plugin` 优先 `Ark Biz` 被加载启动；`Ark Plugin` 之间是双向类索引关系，即可以相互委托对方加载所需的类和资源；`Ark Plugin` 和 `Ark Biz` 是单向类索引关系，即只允许 `Ark Biz` 索引 `Ark Plugin` 加载的类和资源，反之则不允许。

## 场景
SOFAArk 初衷是为了解决包冲突问题，那什么情况下可以使用 SOFAArk 以及如何使用呢？ 假设如下场景，如果工程需要引入两个三方包：A 和 B，但是 A 需要依赖版本号为 0.1 的 C 包，而恰好 B 需要依赖版本号为 0.2 的 C 包，且 C 包的这两个版本无法兼容：

![conflict](https://cdn.yuque.com/lark/2018/png/590/1523868150329-41ea3982-4783-49b0-a1e6-ffffddbe0886.png)

此时，即可使用 SOFAArk 解决该依赖冲突问题；只需要把 A 和版本为 0.1 的 C 包一起打包成一个 Ark 插件，然后让应用工程引入该插件依赖即可；


