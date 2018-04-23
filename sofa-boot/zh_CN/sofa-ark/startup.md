---
title: Ark 容器启动流程
---

# Ark 容器启动流程

Ark 容器的整体启动如下图所述：

![image.png | left | 484x181](https://gw.alipayobjects.com/zos/skylark/7147df70-4f4b-4d45-84be-99af5bca3a45/2018/png/334a5ff9-b15b-4fbe-85a1-d6abe46e7d6d.png "")

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

### 部署 Ark 插件
从 `PluginManagerService` 中获取到所有的 Ark 插件，并按照插件优先级顺序：
* ClassloaderService 准备插件 export 类的 map 映射
* PluginDeployService 启动插件的 `com.alipay.sofa.ark.spi.service.PluginActivator`


### 启动 Ark 业务
从 `BizManagerService` 中获取到所有的 Ark 业务，并执行业务配置在 MANIFEST.MF 属性 Main-Class 中提供的入口 main 函数
