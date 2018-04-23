---
title: Ark 容器类加载机制
---

# Ark 容器类加载机制

Ark 容器中会管理插件和业务，整体的类加载机制可见如下图描述：

![undefined](https://gw.alipayobjects.com/zos/skylark/7dfdc66f-a70d-4ef0-9de3-92b72bf2caf7/2018/png/77f10035-a6c3-4bab-bff3-a2c9a986561f.png)


## Ark 插件类加载机制

每个 Ark 插件都拥有一个独立的类加载器，其类加载的顺序如下：

1. 如果是加载反射生成的字节码，那么会直接抛出 ClassNotFoundException，终止类加载。这一部分主要是来源于我们的工程实践，避免一定找不到的类查找路径过长
2. 查找已经被加载过的类
3. 查找 JDK 中的类，这一块主要包含两部分：第一部分是 ExtClassloader 负责加载的类；第二部分是 JDK 提供的类但从 ExtClassloader 中加载不到，但在本地运行时会被加入到 SystemClassloader 的 classpath 中，同时这些类可能会被放到一些三方工具包中，典型的如 tool.jar 中 `sun.tools.attach.BsdVirtualMachine`,这一部分也主要来源于我们的工程实践，避免类被加载超过一次，从而引发报错
4. 看类是否是由 Sofa Ark 提供的接口类，典型的如: `com.alipay.sofa.ark.spi.service.PluginActivator`, 如果是，则类会委托给 Ark 容器的类加载器加载
5. 看是否在插件的 import 中(包括 import-classes 和 import-package)， 如果在，则会委托给导出该类的插件类加载器加载
6. 在插件自身的 classpath 中加载
7. 如果上述都失败了，则会尝试在 SymtemClassloader 中加载，这一步主要是为了解决使用 agent 时的情形

如果上述的步骤都加载失败了，则抛出 ClassNotFoundException

## Ark 业务类加载机制

每个 Ark 业务都拥有一个独立的类加载器， Ark 业务类加载机制基本上与 Ark 插件保持一致，在上述的7步中，主要是第5步不一样：

对于 Ark 业务而言，并没有提供 import 的配置，而是认为默认 import 所有插件 export 出来的类；但为了一些特殊的业务场景，我们提供了 Deny-import 的配置让业务可以排除掉某些插件导出的类

# Ark 插件资源加载机制
Ark 插件支持导入导出资源，需要在 `sofa-ark-plugin-maven-plugin` 配置相关的导入导出配置；在使用 ClassLoader 加载资源时，存在两种方式查找资源，`ClassLoader.getResource(String)` 和 `ClassLoader.getResources(String)`；

+ `ClassLoader.getResource(String)`: Ark Plugin 在查找单个资源时，会优先委托导出该资源的 Ark Plugin 加载，如果有多个插件同时导出，则优先级高的插件优先导出；如果加载失败或者没有其他 Ark Plugin 导出，则尝试在本 Ark Plugin 查找加载；

+ `ClassLoader.getResources(String)`: Ark Plugin 在查找多个资源时，会从所有导出该资源的 Ark Plugin 加载，同时也会从本 Ark Plugin 加载资源；

# Ark 业务资源加载机制
默认情况下，Ark Biz 会优先加载 Ark Plugin 导出的资源；如果开发者希望只在工程应用内部查找，则可以通过 `sofa-ark-maven-plugin` 配置 `denyImportResources`；如此，Ark Biz 不会从 Ark Plugin 查找该资源，只会在 Ark Biz 内部查找。