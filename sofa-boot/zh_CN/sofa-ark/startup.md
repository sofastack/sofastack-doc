# Ark 容器启动流程

Ark 应用的整体启动流程如下图所述：

<image src="https://user-images.githubusercontent.com/7148759/49442454-86cdec00-f804-11e8-8298-4f6165631298.png" width="600"/>

当用 java -jar 启动 Ark 包 或者 在 IDE 中通过 `SofaArkBootstrap.launch` 启动 Ark 应用时，相应 `Launcher` 入口会负责启动应用，其中会反射调用 `ArkContainer` 的入口，初始化 `ArkService` ，然后依次执行 pipeline，来完成整个 Ark 应用的启动。

## ArkService

Ark Serivce 是 Ark 容器中的服务，底层使用 Guice 对服务进行管理。同时针对服务，提供了生命周期接口 `com.alipay.sofa.ark.spi.service.ArkService`

```java
public interface ArkService {

    /**
     * Ark Service init
     * @throws ArkException
     */
    void init() throws ArkException;

    /**
     * Ark Service dispose
     * @throws ArkException
     */
    void dispose() throws ArkException;

}
```

当服务实现了上述接口时，在 Ark Serivce 容器启动时和停止时会调用相应的生命周期接口

## Pipeline 服务

Pipeline 也是注册在 Ark Service 容器中的一个服务，服务本身是没有顺序和优先级的，在 Pipeline 中会对服务进行一些组装，同时完成整个 Ark 容器的启动

### Archive 解析

在 Pipeline 的最开始，会将运行的 fatjar 进行解析，解析成运行时需要的模型，主要包括 Ark 插件模型和 Ark 业务模型，并将这些模型注册到 Ark Service 中的 `PluginManagerService` 以及 `BizManagerService` 中

### 初始化环境

设置一些运行时需要的默认参数，比如设置 `log4j.ignoreTCL` 为 `true` 让 log4j/log4j2 初始化是日志不要从 `ThreadContextClassloader` 中寻找配置文件([背景](https://github.com/sofastack/sofa-ark/issues/57))

### 注册容器服务

在 Ark 容器中会发布一些服务供其它的插件来使用，比如 `BizDeployer` 来让 SOFAArk 官方插件 [sofa-jarslink](https://github.com/sofastack/sofa-jarslink) 来完成 biz 的动态加载/卸载等


### 部署 Ark 插件
从 `PluginManagerService` 中获取到所有的 Ark 插件，并按照插件优先级顺序：
* ClassloaderService 准备插件 export 类的 map 映射
* PluginDeployService 启动插件的 `com.alipay.sofa.ark.spi.service.PluginActivator`


### 启动 Ark 业务
从 `BizManagerService` 中获取到所有的 Ark 业务，并执行业务配置在 MANIFEST.MF 属性 Main-Class 中提供的入口 main 函数
