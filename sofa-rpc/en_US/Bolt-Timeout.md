## Timeout control 

When using the Bolt protocol for communication, invoke timeout defaults is 3 seconds. You can configure the timeout when referencing the service, and can also configure the timeout period from the dimension of service or method respectively. SOFARPC timeout can be set in milliseconds. 

### Service 

If you need to set the timeout from the dimension of service when publishing a service, just configure the timeout parameter to the corresponding value. 

#### Use XML

If you reference the service using XML, set the value of the `timeout` attribute of the `<sofa:global-attrs>` tag under the `<sofa:binding.bolt>` tag: 

``` Xml 
<sofa:reference interface="com.example.demo.SampleService" id="sampleService"> 
    <sofa:binding.bolt> 
        <sofa:global-attrs timeout="2000"/> 
    </sofa:binding.bolt> 
</sofa:reference> 
``` 

#### Use Annotation

If you reference the service using Annotation, set the value of the `timeout` attribute of `@SofaReferenceBinding`: 

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", timeout = 2000)) 
private SampleService sampleService; 
``` 

#### Use API in Spring environment

If you reference the service in Spring or Spring Boot environment, just set the value of `timeout` attribute of `BoltBindingParam`: 

```java 
BoltBindingParam boltBindingParam = new BoltBindingParam(); 
boltBindingParam.setTimeout(2000) 
``` 

#### Use API in non-Spring environment

If you reference service using the bare API of SOFARPC directly in non-Spring environment, just set the `timeout` attribute of `ConsumerConfig`: 

```java 
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>() 
    .setInterfaceId(SampleService.class.getName()) 
    .setRegistry(registryConfig) 
    .setProtocol("bolt")
    .setTimeout(2000); 
``` 

### Method

If you want to adjust the timeout for a certain method in a service individually, you can set the timeout period from the dimension of method. 

For a method, the timeout period of the method is prioritized. If not set, the timeout period of the service will be used. 

#### Use XML

If you reference service using XML, just set the `timeout` attribute of the corresponding `<sofa: method>`:  

``` XML 
<Sofa: Reference interface = "com.example.demo .SampleService" id="sampleService"> 
    <sofa:binding.bolt> 
        <sofa:method name="hello" timeout="2000"/> 
    </sofa:binding.bolt> 
</sofa:reference> 
``` 

#### Use Annotation

No method is available for setting the method-level timeout with Annotation currenty. 

#### Use API in Spring environment

To reference a service in Spring or Spring Boot environment, you can just set the value of `timeout` attribute of `RpcBindingMethodInfo`

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();

RpcBindingMethodInfo rpcBindingMethodInfo = new RpcBindingMethodInfo();
rpcBindingMethodInfo.setName("hello");
rpcBindingMethodInfo.setTimeout(2000);

List<RpcBindingMethodInfo> rpcBindingMethodInfos = new ArrayList<>();
rpcBindingMethodInfos.add(rpcBindingMethodInfo);

boltBindingParam.setMethodInfos(rpcBindingMethodInfos);
```

#### Use API in non-Spring API environment 

If you reference a service using the bare API of SOFARPC which run in non-Spring environment, just configure the `timeout` attribute of `MethodConfig`: 

```java
MethodConfig methodConfig = new MethodConfig();
methodConfig.setName("hello");
methodConfig.setTimeout(2000);

List<MethodConfig> methodConfigs = new ArrayList<MethodConfig>();
methodConfigs.add(methodConfig);

ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setMethods(methodConfigs);
```