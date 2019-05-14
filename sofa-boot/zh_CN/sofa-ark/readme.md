## 产品描述
SOFAArk 是一款基于 Java 实现的轻量级类隔离容器，主要提供类隔离和应用(模块)合并部署能力，由蚂蚁金服公司开源贡献；

在大型软件开发过程中，通常会推荐底层功能插件化，业务功能模块化的开发模式，以期达到低耦合、高内聚、功能复用的优点。基于此，SOFAArk 提供了一套较为规范化的插件化、模块化的开发方案，产品能力主要包括：
+ 定义类加载模型，运行时底层插件、业务应用(模块)之间均相互隔离，单一插件和应用(模块)由不同的 ClassLoader 加载，可以有效避免相互之间的包冲突，提升插件和模块功能复用能力；
+ 定义插件开发规范，提供 maven 打包工具，简单快速将多个二方包打包成插件（Ark Plugin，以下简称 Plugin）
+ 定义模块开发规范，提供 maven 打包工具，简单快速将应用打包成模块 (Ark Biz，以下简称 Biz)
+ 针对 Plugin、Biz 提供标准的编程界面，包括服务、事件、扩展点等机制
+ 支持多 Biz 的合并部署，开发阶段将多个 Biz 打包成可执行 Fat Jar，或者运行时使用 API 或配置中心(Zookeeper)动态地安装卸载 Biz

基于以上能力，SOFAArk 可以帮助解决依赖包冲突、多应用(模块)合并部署等场景问题。

## 场景
#### 包冲突
日常使用 Java 开发，常常会遇到包依赖冲突的问题，尤其当应用变得臃肿庞大，包冲突的问题也会变得更加棘手，导致各种各样的报错，例如 `LinkageError`, `NoSuchMethodError` 等；实际开发中，可以采用多种方法来解决包冲突问题，比较常见的是类似 Spring Boot 的做法，统一管理应用所有依赖包的版本，保证这些三方包不存在依赖冲突；这种做法只能有效避免包冲突问题，不能根本上解决包冲突的问题；如果某个应用的确需要在运行时使用两个相互冲突的包，例如 `protobuf2` 和 `protobuf3`，那么类似 Spring Boot 的做法依然解决不了问题。

为了彻底解决包冲突的问题，需要借助类隔离机制，使用不同的 `ClassLoader` 加载不同版本的三方依赖，进而隔离包冲突问题； `OSGI` 作为业内最出名的类隔离框架，自然是可以被用于解决上述包冲突问题，但是 `OSGI` 框架太过臃肿，功能繁杂；为了解决包冲突问题，引入 `OSGI` 框架，有牛刀杀鸡之嫌，且反而使工程变得更加复杂，不利于开发；

SOFAArk 采用轻量级的类隔离方案来解决日常经常遇到的包冲突问题，在蚂蚁金服内部服务于整个 [SOFABoot](https://github.com/sofastack/sofa-boot) 技术体系，弥补 Spring Boot 没有的类隔离能力。SOFAArk 提出了一种特殊的包结构 -- Ark Plugin，在遇到包冲突时，用户可以使用 Maven 插件将若干冲突包打包成 Plugin，运行时由独立的 PluginClassLoader 加载，从而解决包冲突。

假设如下场景，如果工程需要引入两个三方包：A 和 B，但是 A 需要依赖版本号为 0.1 的 C 包，而恰好 B 需要依赖版本号为 0.2 的 C 包，且 C 包的这两个版本无法兼容：

![conflict](https://cdn.yuque.com/lark/2018/png/590/1523868150329-41ea3982-4783-49b0-a1e6-ffffddbe0886.png)

此时，即可使用 SOFAArk 解决该依赖冲突问题；只需要把 A 和版本为 0.1 的 C 包一起打包成一个 Ark 插件，然后让应用工程引入该插件依赖即可；

#### 合并部署
复杂项目通常需要跨团队协作开发，各自负责不同的组件，而众所周知，协调跨团队合作开发会遇到不少问题；比如各自技术栈不统一导致的依赖冲突，又比如往同一个 Git 仓库提交代码常常导致 merge 冲突。因此，如果能让每个团队将负责的功能组件当成一个个单独的应用开发，运行时合并部署，通过统一的编程界面交互，那么将极大的提升开发效率及应用可扩展性。SOFAArk 提出了一种特殊的包结构 -- Ark Biz，用户可以使用 Maven 插件将应用打包成 Biz，允许多 Biz 在 SOFAArk 容器之上合并部署，并通过统一的编程界面交互。

##### 静态合并部署
SOFAArk 提供了静态合并部署能力，在开发阶段，应用可以将其他应用打成的 Biz 包通过 Maven 依赖的方式引入，而当自身被打成可执行 Fat Jar 时，可以将其他应用 Biz 包一并打入，启动时，则会根据优先级依次启动各应用。每个 Biz 使用独立的 BizClassLoader 加载，不需要考虑相互依赖冲突问题，Biz 之间则通过 `SofaService/SofaRefernece` JVM 服务进行交互。

##### 动态合并部署
动态合并部署区别于静态合并部署最大的一点是，运行时通过 API 或者配置中心（Zookeeper）来控制 Biz 的部署和卸载。动态合并部署的设计理念图如下：

![life-arch](../resources/life-arch.png)

无论是静态还是动态合并部署都会有宿主应用（master biz）的概念, 如果 Ark 包只打包了一个 Biz，则该 Biz 默认成为宿主应用；如果 Ark 包打包了多个 Biz 包，需要配置指定宿主应用。宿主应用不允许被卸载，一般而言，宿主应用会作为流量入口的中台系统，具体的服务实现会放在不同的动态 Biz 中，供宿主应用调用。宿主应用可以使用 SOFAArk 提供的客户端 API 实现动态应用的部署和卸载。除了 API, SOFAArk 提供了 Config Plugin，用于对接配置中心（目前支持 Zookeeper），运行时接受动态配置；Config Plugin 会解析下发的配置，控制动态应用的部署和卸载。

## 原理
SOFAArk 包含三个概念，`Ark Container`, `Ark Plugin` 和 `Ark Biz`; 运行时逻辑结构图如下:

![image.png | center | 1310x1178](https://cdn.yuque.com/lark/2018/png/590/1523868989241-f50695ed-dca0-4bf7-a6a9-afe07c2ade76.png)

在介绍这三个概念之前，先介绍上述 `Ark` 包概念；`Ark` 包是满足特定目录格式要求的可运行 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将**单个或多个应用**打包成标准格式的 `Ark` 包；使用 `java -jar` 命令即可在 SOFAArk 容器之上启动所有应用；`Ark` 包通常包含 `Ark Container`、`Ark Plugin` 和 `Ark Biz`；以下我们针对这三个概念简单做下名词解释：

+ `Ark Container`: SOFAArk 容器，负责 `Ark` 包启动运行时的管理；`Ark Plugin` 和 `Ark Biz` 运行在 SOFAArk 容器之上；容器具备管理插件和应用的功能；容器启动成功后，会自动解析 classpath 包含的 `Ark Plugin` 和 `Ark Biz` 依赖，完成隔离加载并按优先级依次启动之；

+ `Ark Plugin`: Ark 插件，满足特定目录格式要求的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-plugin-maven-plugin` 可以将一个或多个普通的 `Java jar` 打包成一个标准格式的 `Ark Plugin`；`Ark Plugin` 会包含一份配置文件，通常包括插件类导入导出配置、资源导入导出配置、插件启动优先级等；运行时，SOFAArk 容器会使用独立的 `PluginClassLoader`加载插件，并根据插件配置构建类加载索引表、资源加载索引表，使插件和插件之间、插件和应用之间相互隔离；

+ `Ark Biz`: Ark 应用模块，满足特定目录格式要求的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成一个标准格式的 `Ark Biz`；`Ark Biz` 是工程应用以及其依赖包的组织单元，包含应用启动所需的所有依赖和配置；一个 Ark 包中可以包含多个 `Ark Biz` 包，按优先级依次启动，Biz 之间通过 JVM 服务交互；

运行 `Ark` 包，`Ark Container` 优先启动，容器自动解析 `Ark` 包中含有的 `Ark Plugin` 和 `Ark Biz`，并读取他们的配置信息，构建类和资源的加载索引表；然后使用独立的 `ClassLoader` 加载并按优先级配置依次启动；需要指出的是，`Ark Plugin` 优先 `Ark Biz` 被加载启动；`Ark Plugin` 之间是双向类索引关系，即可以相互委托对方加载所需的类和资源；`Ark Plugin` 和 `Ark Biz` 是单向类索引关系，即只允许 `Ark Biz` 索引 `Ark Plugin` 加载的类和资源，反之则不允许。


