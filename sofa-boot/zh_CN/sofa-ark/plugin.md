---
title: Ark 容器插件机制
---

## Ark 插件启动

Ark 中提供了插件启动的接口 `com.alipay.sofa.ark.spi.service.PluginActivator`  ，其定义如下：

```java
public interface PluginActivator {

    /**
     * Start Plugin
     * @param context plugin context
     * @throws ArkException
     */
    void start(PluginContext context) throws ArkException;

    /**
     * Stop Plugin
     * @param context
     * @throws ArkException
     */
    void stop(PluginContext context) throws ArkException;

}
```

插件只需要实现此接口，并在 MANIFEST.MF 中配置 `activator` 属性，就会在启动时执行 `start` 方法，停止时执行 `stop` 方法

## Ark 插件通信

Ark 之间的通信是通过服务来完成的， 在上述启动接口方法的入参类型 `com.alipay.sofa.ark.spi.model.PluginContext` 中提供了发布服务和引用服务的接口

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
     * @param pluginName the name of the plugin which publish the service
     * @return service reference
     */
    <T> ServiceReference<T> referenceService(Class<T> ifClass, String pluginName);

    /**
     * Get Service List publish by plugin
     * @param ifClass service interface
     * @param <T>
     * @return
     */
    <T> List<ServiceReference<T>> referenceServices(Class<T> ifClass);
```

插件服务是以接口为粒度的，针对同一个接口：
* 每一个插件只允许发布一个服务，重复发布则会直接返回已经发布服务的引用
* 当多有个插件发布服务时，若通过 `referenceService`  引用单个服务
  * 当不指定 pluginName 时，则返回优先级最高的服务
  * 当指定 pluginName 时，则返回该插件发布的服务


返回的服务引用 `ServiceReference` 定义如下:

```plain
public interface ServiceReference<T> {

    /**
     * get Service Object
     * @return service
     */
    T getService();

    /**
     * get Service Metadata
     * @return
     */
    ServiceMetadata getServiceMetadata();

}

public interface ServiceMetadata {

    /**
     * get Service Unique Name
     * @return service name
     */
    String getServiceName();

    /**
     * get Service Interface Class
     * @return interface class
     */
    Class<?> getInterfaceClass();

    /**
     * get ServiceProvider
     * @return
     */
    ServiceProvider getServiceProvider();

}
```

其中通过
* `getService()` 可以获取到服务的实体 (也即发布服务时的 implObject)
* `getServiceMetadata()` 可以获取到服务的元数据信息，包括
  * 服务名：即服务的接口名
  * 服务接口
  * 服务的提供方：包括提供方名字(插件名等)、提供方优先级(插件优先级)


