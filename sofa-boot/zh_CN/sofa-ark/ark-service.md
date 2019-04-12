SOFAArk 定义了两种服务类型，用于解决应用和插件，应用和应用之间的通信问题，下面分别介绍这两种服务类型：

### 插件服务
SOFAArk 允许在 Plugin 通过 PluginContext 发布和引用服务，也可以使用注解 @ArkInject 引用服务。为了方便开发高级特性，SOFAArk 容器默认将内部功能组件发布成了服务，包括 Biz 管理，Plugin 管理，事件管理，服务注册管理。目前不允许 Biz 发布服务，只能引用插件服务。下面介绍如何发布和引用插件服务，以及 SOFAArk 容器默认发布的服务。

#### 发布服务
每个 Plugin 都可以定义唯一的插件入口，需要实现 `PluginActivator` 接口并在打包插件配置中声明，先看下接口定义：

```java
public interface PluginActivator {

    /**
     * Start Plugin
     * @param context plugin context
     * @throws ArkRuntimeException
     */
    void start(PluginContext context);

    /**
     * Stop Plugin
     * @param context
     * @throws ArkRuntimeException
     */
    void stop(PluginContext context);
}
```

SOFAArk 容器在启动插件时，会调用插件启动入口(如果有)，因此如果插件实现方需要发布插件服务供其他插件或者 Biz 调用，可以使用入参 `PluginContext` 发布服务，`PluginContext` 提供了两个方法发布服务：

```java
    /**
     * Publish Plugin Service
     * @param ifClass service interface
     * @param implObject service implement object
     * @param <T>
     * @return
     */
    <T> ServiceReference<T> publishService(Class<T> ifClass, T implObject);

    /**
     * Publish Plugin Service
     * @param ifClass service interface
     * @param implObject service implement object
     * @param uniqueId service implementation id
     * @param <T>
     * @return
     */
    <T> ServiceReference<T> publishService(Class<T> ifClass, T implObject, String uniqueId);
```

这两个方法的区别在于是否指定 `uniqueId`，因为同一个接口，可能会有多个服务实现，因此需要使用 `uniqueId` 区别，同样在引用端也需要指定 `uniqueId`. 默认 `uniqueId` 为空

#### 引用服务
SOFAArk 提供了两种方式引用插件服务，在插件内部，可以直接使用 `PluginContext` 引用服务，`PluginContext` 提供了两个简单的方法引用服务：

```java
    /**
     * Get Service publish by plugin, when there are multiple services, return the highest priority plugin service
     * @param ifClass service interface
     * @param <T>
     * @return service reference
     */
    <T> ServiceReference<T> referenceService(Class<T> ifClass);

    /**
     * Get Service publish  by one specific plugin
     * @param ifClass service interface
     * @param <T>
     * @param uniqueId service implementation
     * @return service reference
     */
    <T> ServiceReference<T> referenceService(Class<T> ifClass, String uniqueId);
```

在 Biz 内部，如果是 Spring Boot/SOFABoot 应用，可以直接使用注解 `@ArkInject` 引用服务，注解声明如下：

```java
@java.lang.annotation.Target(ElementType.FIELD)
@java.lang.annotation.Retention(java.lang.annotation.RetentionPolicy.RUNTIME)
@java.lang.annotation.Documented
public @interface ArkInject {
    /**
     * ark service interface
     * @return
     */
    Class<?> interfaceType() default void.class;

    /**
     * ark service uniqueId
     *
     * @return return reference unique-id
     */
    String uniqueId() default "";
}
```

SOFAArk 提供集成 Spring Boot/SOFABoot 功能，在 Field 上打上 `@ArkInject`，指定接口类型和 uniqueId 即可完成自动注入。为了更加方便的使用，如果没有指定 `interfacetType` 类型，默认使用被打注解的 Field 类型。


在插件内部，有时候也可以使用 `@ArkInject` 引用服务，即插件在发布某个服务时，服务内部可以直接使用 `@ArkInject` 引用服务；需要注意的是，被引用的服务如果是其他插件发布的，则必须满足其他插件优先当前插件启动。

#### 默认服务
前面提到，为了方便 Plugin 和 Biz 开发高级特性，SOFAArk 将内部功能组件发布成服务，包括：

+ BizManageService
> Biz 管理器，管理、查询 Biz 信息

+ BizFactoryService
> Biz 解析器，解析 Biz 文件

+ PluginManagerService
> Plugin 管理器，管理、查询 Plugin 信息

+ PluginFactoryService
> Plugin 解析器，解析 Plugin 文件

+ EventAdminService
> SOFAArk 事件管理器，管理事件监听器以及发布事件

+ RegistryService
> 插件服务管理器，真正用于发布和引用插件服务, 不推荐 Biz 使用它来发布服务，Biz 只能引用服务，否则可能导致 Biz 不能完全卸载

上述服务有 SOFAArk 容器默认发布，其他 Plugin 和 Biz 可以引用，建议在了解原理之后使用

### 应用服务
插件服务更多是为了解决 Plugin 和 Biz 的通信问题，Biz 之间的通信则依赖 SOFABoot 提供的 runtime-sofa-boot-plugin，它提供了 `SofaService/SofaReference`，具体参考[服务通信](./ark-jvm)文档。