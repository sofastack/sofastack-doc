## Starting an Ark plug-in

Ark provides the interface for starting a plug-in `com.alipay.sofa.ark.spi.service.PluginActivator`. The definition of the interface is as follows:

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

Once a plug-in implements this interface, and the `activator` attribute is configured in MANIFEST.MF, the plug-in will use the `start` method when it starts and the `stop` method when it stops.

## Ark plug-in communication

Ark plug-ins communicate using services. The interfaces used by the publishing and reference services are provided in the input parameter type `com.alipay.sofa.ark.spi.model.PluginContext` of the preceding method of starting an interface.

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

A plug-in service is interface-specific. For one interface, the following descriptions are true:
* Only one service can be published for each plug-in. When more than one service is published, the reference of the previously published service will be returned.
* If you use `referenceService` to reference a single service when multiple plug-ins have published services, which service is returned depends on whether pluginName is specified:
  * When not specified, the service with the highest priority is returned.
  * When specified, the service published by the plugin with the specified name is returned.


The returned service reference `ServiceReference` is defined as follows:

```java
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

Where:
* `getService()` can be used to obtain the service object (specifically, implObject is used when the service is published).
* `getServiceMetadata()` can be used to obtain the service metadata, including:
  * Service name: interface name of the service
  * Service interface
  * Service provider information: includes provider name (such as the plugin name) and provider priority (plugin priority)



