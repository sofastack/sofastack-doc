## Basic usage of Bolt protocol

### Publish Service

To use SOFARPC to publish a Bolt-protocol service, you only need to add a Binding named bolt. The ways to add Bolt Binding are as follows:

#### XML

To publish a Bolt service using XML, simply add the `<sofa:binding.bolt>` tag to the `<sofa:service>` tag:

```xml
<sofa:service ref="sampleService" interface="com.alipay.sofa.rpc.sample.SampleService">
    <sofa:binding.bolt/>
</sofa:service>
```

#### Annotation

To publish a Bolt service using Annotation, you only need to set the `bindingType` of `@SofaServiceBinding` to `bolt`:

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "bolt")})
Public class SampleServiceImpl implements SampleService {
}
```

#### API in Spring environment 

To publish a Bolt-protocol service in Spring or Spring Boot environment, just add `BoltBindingParam` to `ServiceParam`:

```java
ServiceParam serviceParam = new ServiceParam();
serviceParam.setInterfaceType(SampleService.class); // Set the service interface
serviceParam.setInstance(new SampleServiceImpl()); // Set the implementation of the service interface
            
List<BindingParam> params = new ArrayList<BindingParam>();
BindingParam serviceBindingParam = new BoltBindingParam();
Params.add(serviceBindingParam);
serviceParam.setBindingParams(params);
```

#### API in non-Spring environment

To provide the Bolt-protocol service using the bare API of SOFARPC in a non-Spring environment, just set the `ServerConfig` whose protocol is Bolt to the corresponding `ProviderConfig`:

```java
RegistryConfig registryConfig = new RegistryConfig()
        .setProtocol("zookeeper")
        .setAddress("127.0.0.1:2181");
// Create a new ServerConfig with Bolt protocol
ServerConfig serverConfig = new ServerConfig()
        .setPort(8803)
        .setProtocol("bolt");
ProviderConfig<SampleService> providerConfig = new ProviderConfig<SampleService>()
        .setInterfaceId(SampleService.class.getName())
        .setRef(new SampleServiceImpl())
        .setServer(serverConfig) // Set ServerConfig to ProviderConfig to indicate that the protocol published by this service is Bolt.
        .setRegistry(registryConfig);
providerConfig.export();
```

### Reference Service

To reference a Bolt-protocol service using SOFARPC, just add a Binding named bolt. The ways to use Bolt Binding are as follows:

#### XML

To reference a Bolt-protocol service using XML, simply add the `<sofa:binding.bolt>` tag to the `<sofa:reference>` tag:

```xml
<sofa:reference id="sampleService" interface="com.alipay.sofa.rpc.sample.SampleService">
    <sofa:binding.bolt/>
</sofa:reference>
```

#### Annotation

To reference a Bolt-protocol service using Annotation, just set the `bindingType` of `@SofaReferenceBinding` to `bolt`:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt"))
Private SampleService sampleService;
```

#### API in Spring environment 

To reference a Bolt-protocol service in a Spring or Spring Boot environment, simply add a `BoltBindingParam` to `ReferenceParam`:

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);
ReferenceParam<SampleService> referenceParam = new ReferenceParam<SampleService>();
referenceParam.setInterfaceType(SampleService.class);

BindingParam refBindingParam = new BoltBindingParam();
referenceParam.setBindingParam(refBindingParam);
```

#### API in non-Spring environment

To reference a Bolt-protocol service using the bare API of SOFARPC in a non-Spring environment, you only need to set the `ConsumerConfig` protocol to `bolt`:

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt");
SampleService sampleService = consumerConfig.refer();
```