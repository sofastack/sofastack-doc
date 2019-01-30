## Service publishing/reference

The basic configuration for SOFARPC service publishing and reference is described in the "Programming Interface" chapter. Here are some of the features of service publishing and referencing.

### One service publishes multiple protocols

In SOFARPC, a service can be published as multiple protocols, which allows the callers to call the service provider using different protocols.

If you use the Java API, you can build multiple ServerConfigs as follows to set different protocols for different ServerConfigs and then assign these ServerConfigs to ProviderConfig:

```java
 List<ServerConfig> serverConfigs = new ArrayList<ServerConfig>();
 serverConfigs.add(serverConfigA);
 serverConfigs.add(serverConfigB);
 providerConfig.setServer(serverConfigs);
```

If you use XML, add multiple bindings directly in the `<sofa:service>` tag:

```xml
<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt/>
    <sofa:binding.rest/>
    <sofa:binding.dubbo/>
</sofa:service>
```

If you use annotation, add multiple bindings in `@SofaService`:

```java
@SofaService (
        interfaceType = SampleService.class,
        bindings = {
                @SofaServiceBinding(bindingType = "rest"),
                @SofaServiceBinding(bindingType = "bolt")
        }
)
public class SampleServiceImpl implements SampleService {
    // ...
}
```

### One service registers multiple registry centers

If you use the API, build multiple RegistryConfigs and assign them to ProviderConfig:

```java
List<RegistryConfig> registryConfigs = new ArrayList<RegistryConfig>();
registryConfigs.add(registryA);
registryConfigs.add(registryB);
providerConfig.setRegistry(registryConfigs);
```

### Method-level parameter settings

In the Java API mode, you can set the corresponding parameters by calling the `set` method of the MethodConfig object, as shown below:

```java
MethodConfig methodConfigA = new MethodConfig();
MethodConfig methodConfigB = new MethodConfig();

List<MethodConfig> methodConfigs = new ArrayList<MethodConfig>();
methodConfigs.add(methodConfigA);
methodConfigs.add(methodConfigB);
        
providerConfig.setMethods(methodConfigs); //server settings
consumerConfig.setMethods(methodConfigs); //Client settings
```

In the XML mode, you can use the `<sofa:method>` tag in the corresponding binding to set the corresponding parameters:

```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000" address-wait-time="2000"/> <!-- Call timeout; address wait time. -->
        <sofa:route target-url="127.0.0.1:22000"/> <!-- Directly connected address -->
        <sofa:method name="sayName" timeout="3000"/> <!-- Method level configuration -->
    </sofa:binding.bolt>
</sofa:reference>

<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000"/>
        <sofa:method name="sayName" timeout="2000"/>
    </sofa:binding.bolt>
</sofa:service>
```

Currently, the Annotation mode does not support setting method-level parameters, which will be available in the future versions.

### Configuration overrides

Both the service provider and service caller support some configurations in SOFARPC, such as the calling timeout attribute. The priority of these configurations is as follows:

Thread call-level settings >> Service caller method-level settings >> Service caller reference-level settings >> Service provider method-level settings >> Service provider service-level settings