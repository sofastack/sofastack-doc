## 直连调用

SOFARPC 支持指定地址进行调用的场景。用 Java API 的使用方式如下，设置直连地址即可：

```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()        
            .setInterfaceId(HelloService.class.getName())        
            .setRegistry(registryConfig)        
            .setDirectUrl("bolt://127.0.0.1:12201");
```

用 XML 的使用方式如下：

```xml
<sofa:reference interface="com.alipay.sample.HelloService" id="helloService">
    <sofa:binding.bolt>
        <sofa:route target-url="127.0.0.1:12200"/>
    </sofa:binding.bolt>
</sofa:reference>
```

用 Annotation 的使用方式如下：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", directUrl = "127.0.0.1:12220"))
private SampleService sampleService;
```
