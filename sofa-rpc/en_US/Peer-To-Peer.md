## Peer to peer

SOFARPC supports scenarios where a specified address is called.

The use of direct call in Java API is as follows, only set the direct connection address:

```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()        
            .setInterfaceId(HelloService.class.getName())        
            .setRegistry(registryConfig)        
            .setDirectUrl("bolt://127.0.0.1:12201");
```

The use of direct call in XML is as follows:

```xml
<sofa:reference interface="com.alipay.sample.HelloService" id="helloService">
    <sofa:binding.bolt>
        <sofa:route target-url="127.0.0.1:12200"/>
    </sofa:binding.bolt>
</sofa:reference>
```

The use of direct call in Annotation is as follows:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", directUrl = "127.0.0.1:12220"))
private SampleService sampleService;
```