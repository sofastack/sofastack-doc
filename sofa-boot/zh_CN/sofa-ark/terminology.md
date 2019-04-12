### Ark 包
SOFAArk 定义特殊格式的**可执行 Jar 包**，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成一个标准格式的 `Ark 包`；使用命令 `java -jar` 即可在 SOFAArk 容器之上启动应用；`Ark 包` 通常包含 `Ark Container` 、`Ark Plugin` 依赖（如果有）、合并部署的 `Ark Biz` （如果有）以及应用自身的 `Ark Biz`；详情可参考 [Ark 包](./ark-jar)；

### Ark Container
Ark 容器，`Ark Plugin` 和 `Ark Biz` 运行在 SOFAArk 容器之上；容器具备管理多插件、多应用的功能；容器启动成功后，会解析 `Ark Plugin` 和 `Ark Biz` 配置，完成隔离加载并按优先级依次启动之；Ark Container 一般不会被用户直接感知，由打包插件 `sofa-ark-maven-plugin` 自动打入。详情可参考 [SOFAArk 容器启动](./startup)；

### Ark Plugin
Ark 插件，SOFAArk 定义特殊格式的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-plugin-maven-plugin` 可以将一个或多个普通的 `Java  Jar` 包打包成一个标准格式的 `Ark Plugin`； `Ark Plugin` 会包含一份配置文件，通常包括插件类和资源的导入导出配置、插件启动优先级等；运行时，Ark 容器会使用独立的 `PluginClassLoader` 加载插件，并根据插件配置构建类加载索引表，从而使插件与插件、插件与应用之间相互隔离；详情可参考 [Ark Plugin](./ark-plugin)；

### Ark Biz
Ark 模块，SOFAArk 定义特殊格式的 `Fat Jar` ，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成一个标准格式的 `Ark Biz` 包；作用有二点，一、在 `Ark 包` 中，作为工程应用模块及其依赖包的组织单元；二、可以被其他应用当成普通 Jar 包依赖，用于在同一个 SOFAArk 容器启动多个 `Ark Biz`；多个 `Ark Biz` 共享 `Ark Container` 和 `Ark Plugin` ；详情可参考 [Ark Biz](./ark-biz)；

### 合并部署
SOFAArk 允许将多个应用（Biz 包）合并打入到 Ark 包中，当启动 Ark 包时，会启动所有应用；也支持在运行时通过 API 或者配置中心（例如 Zookeeper）动态的部署和卸载应用，这些应用同时运行在同一个 JVM 中，由独立的 BizClassLoader 加载，各应用之间通过 `SofaService/SofaReference` 实现交互，称之为多应用的合并部署。

### 宿主应用
宿主应用是相对合并部署而言，在打包 Ark 包时，至少有一个 Biz 包被打入，如果应用引入了其他 Biz 包，则 Ark 包中会存在多个 Biz 包。当只有一个 Biz 包时，默认将其设置为宿主应用；如果存在多个 Biz 包，则需要配置指定宿主应用。宿主应用相对其他 Biz 包最大的不同，即不允许被卸载。


简单总结下，在 SOFAArk 框架中，应用(配置、源码、依赖)被打包成 Biz 包组织在一起，但是特殊的依赖（Ark Plugin 和其他应用 Biz 包）不会被打入 Biz 包中，Biz 包是**不可执行的 Fat Jar**; Ark Plugin 是特殊的二方包，可以将多个二方依赖打包成 Plugin，运行时由独立的 PluginClassLoader 加载，根据打包时配置的导出导入资源、类，构建运行时类加载模型；Ark 包是**可执行 Fat Jar**，一般由 Ark Container、Ark Plugin(0个或多个)、Ark Biz(至少一个)。
