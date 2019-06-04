SOFABoot provides developers with three ways to publish and reference JVM services

- XML
- Annotation
- Programming API

### XML

# Service Publish

First, we need to define a Bean:

```xml
<bean id="sampleService" class="com.alipay.sofa.runtime.test.service.SampleServiceImpl">
```

Then, publish the Bean as a SOFA JVM service by using the Spring extension tag provided by SOFA.

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService">
	<sofa:binding.jvm/>
</sofa:service>
```

In the preceding configuration, the interface parameter indicates the interface for releasing services, and the ref parameter indicates the Bean to be published as a JVM service.

At this point, we have published a JVM service success.

#### Service Reference

We can also reference a JVM service by using the Spring extension tag provided by SOFA.

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleServiceRef">
	<sofa:binding.jvm/>
</sofa:reference>
```

In the preceding configuration, the interface parameter indicates the service interface, which must be consistent with that configured during the service publish. The meaning of the ID attribute is the same as Spring BeanId. A Spring Bean with the ID sampleServiceRef will be generated from the above configuration. We can inject it anywhere in the Spring context of the current SOFABoot module.

> service/reference tag also supports RPC service publish, with related document: [RPC Service Publish and Reference](https://github.com/sofastack/sofa-rpc/wiki/Publish-And-Reference)

### Annotation

> **Warning**
>
> If a service has been annotated with @SofaService, it cannot be published in the mode of XML. Choose one mode to publish the service instead of a mixture of two modes.

In addition to publishing JVM services and reference through XML, SOFABoot also provides Annotation for JVM services publish and reference. To publish JVM services through Annotation, we only need to add an annotation @SofaService to the implementation class, as follows:

```java
@SofaService
public class SampleImpl implements SampleInterface {
   public void test() {

   }
}
```

> **Prompt**
>
> @SofaService is used to publish a Spring Bean as a JVM service, which means that although you may not write the configuration of \<sofa:service/\>, you still need to configure the class annotated with @SofaService as a Spring Bean.

When configuring \<sofa:service/\> in the XML mode, you have configured an interface for the service. However, when using the @SofaService annotation, you haven't configured the service interface. This is because when the class annotated with @SofaService has implemented only one interface, the framework directly uses the interface as the service interface. What if the class annotated with @SofaService has implemented multiple interfaces? In this case, you can set the interfaceType field of @SofaService to specify the service interface, as shown below:

```java
@SofaService(interfaceType=SampleInterface.class)
public class SampleImpl implements SampleInterface, Serializable {
   public void test() {

   }
}
```

Corresponding to @SofaService, SOFABoot provides @SofaReference to reference a JVM service. Suppose we need to use a JVM service called SampleService in a Spring Bean, we only need to add an annotation of @SofaReference to the field:

```java
public class SampleServiceRef {
    @SofaReference
    private SampleService sampleService;
}
```

Similar to @SofaService, you do not have to specify the service interface for @SofaReference. This is because when no service interface is specified for @SofaReference, SOFABoot directly uses the type of the annotated field as the service interface. Alternatively, you can also set the interfaceType property of @SofaReference to specify, as shown below:

```java
public class SampleServiceRef {
    @SofaReference(interfaceType=SampleService.class)
    private SampleService sampleService;
}
```

### Programming API

SOFABoot provides a mode of programming API for JVM service publish and reference, making it easy to directly publish and reference JVM services in code. Similar to the ApplicationContextAware of Spring, to use the programming API mode, first we need to implement the ClientFactoryAware interface to get the programming component API:

```java
public class ClientFactoryBean implements ClientFactoryAware {
    private ClientFactory clientFactory;

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

Take SampleService as an example, let's see how we can use clientFactory to publish JVM services through programming API:

```java
ServiceClient serviceClient = clientFactory.getClient(ServiceClient.class);

ServiceParam serviceParam = new ServiceParam();
serviceParam.setInstance(new SampleServiceImpl());
serviceParam.setInterfaceType(SampleService.class);
serviceClient.service(serviceParam);
```

In the above code

1. Get the ServiceClient object through the clientFactory
2. Construct the ServiceParam object, which contains the parameters required for publishing the service. Set the object to be published through setInstance method, and set the service interface through setInterfaceType method.
3. Publish a JVM service by calling the service method of ServiceClient

The code for JVM service reference through programming API is similar:

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);

ReferenceParam<SampleService> referenceParam = new ReferenceParam<SampleService>();
referenceParam.setInterfaceType(SampleService.class);
SampleService proxy = referenceClient.reference(referenceParam);
```

Similar to publishing a service, to reference a JVM service, simply retrieve the ReferenceClient from the ClientFactory object, construct a ReferenceParam, configure the service interface, and call the reference method of ReferenceClient.

> **Prompt**
> 
> The Reference object created by dynamic client is very heavy. Do not create it frequently in actual practice and cache it in advance, otherwise there may be a risk of memory leak.

### uniqueId

Sometimes, you may need to publish two services at the same time, which correspond to different implementations. By using the preceding sampleService as an example, you may have two SampleService implementations, both of which need to be published as SOFA JVM Services. By using the XML mode, you can configure as follows:

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService1">
</sofa:service>
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService2">
</sofa:service>
```

The service can be correctly published but its reference is confusing, in which case the following configuration is used:

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService">
</sofa:reference>
```

However, you have no idea which JVM service is referenced by the preceding JVM.

To address such issues, SOFA introduces the uniqueId concept, which helps identify service-interface-like JVM services. By introducing the uniqueId to the preceding service publish code, the code is modified as follows:

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService1" unique-id="ss1">
</sofa:service>
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService2" unique-id="ss2">
</sofa:service>
```

Then, to use sampleService1 during service reference, set the unique-id to ss1, as shown in the following example:

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService" unique-id="ss1">
</sofa:reference>
```

To use sampleService2, set the unique-id to ss2, as shown below:

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService" unique-id="ss2">
</sofa:reference>
```

As mentioned above, uniqueId is used in the XML mode. When we use the Annotation mode to publish or reference JVM services, we can set uniqueID by setting the uniqueID attribute of @SofaService and @SofaReference. When we use the programming API mode to publish or reference JVM services, we can set the uniqueId through setUniqueId of ServiceParam and ReferenceParam.
