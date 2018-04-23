---
title: 名词解释
---

### Ark 包
满足特定目录格式要求的 `Executed Fat Jar` ，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin`可以将工程应用打包成一个标准格式的 `Ark 包`；使用命令 `java -jar` 即可在 SOFAArk 容器之上启动应用；`Ark 包` 通常包含 `Ark Container` 、`Ark Plugin` 依赖（如果有）、合并部署的 `Ark Biz` （如果有）以及应用自身的 `Ark Biz`；详情可参考 [Ark 包](sofastack.github.io/docs/ark-jar.html)；

### Ark Container
Ark 容器，`Ark Plugin` 和 `Ark Biz` 运行在 SOFAArk 容器之上；容器具备管理多插件、多应用的功能；容器启动成功后，会解析 `Ark Plugin` 和 `Ark Biz` 配置，完成隔离加载并按优先级依次启动之；详情可参考 [SOFAArk 容器启动](sofastack.github.io/docs/startup.html)；

### Ark Plugin
Ark 插件，满足特定目录格式要求的 `Fat Jar`，使用官方提供的 `Maven` 插件 `sofa-ark-plugin-maven-plugin` 可以将一个或多个普通的 `Java  Jar` 包打包成一个标准格式的 `Ark Plugin`； `Ark Plugin` 会包含一份配置文件，通常包括插件类和资源的导入导出配置、插件启动优先级等；运行时，Ark 容器会使用独立的 `PluginClassLoader` 加载插件，并根据插件配置构建类加载索引表，从而使插件与插件、插件与应用之间相互隔离；详情可参考 [Ark Plugin](sofastack.github.io/docs/ark-plugin.html)；

### Ark Biz
Ark 模块，满足特定目录格式要求的 `Fat Jar` ，使用官方提供的 `Maven` 插件 `sofa-ark-maven-plugin` 可以将工程应用打包成一个标准格式的 `Ark Biz` 包；作用有二点，一、在 `Ark 包` 中，作为工程应用模块及其依赖包的组织单元；二、可以被其他应用当成普通 Jar 包依赖，用于在同一个 SOFAArk 容器启动多个 `Ark Biz`；多个 `Ark Biz` 共享 `Ark Container` 和 `Ark Plugin` ；详情可参考 [Ark Biz](sofastack.github.io/docs/ark-biz.html)；


