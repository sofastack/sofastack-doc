## 超时控制

使用 Bolt 协议进行通信的时候，SOFARPC 的超时时间默认为 3 秒，用户可以在引用服务的时候去设置超时时间，又分别可以在服务以及方法的维度设置超时时间，SOFARPC 的超时时间的设置的单位都为毫秒。

### 服务维度

如果需要在发布服务的时候在服务维度设置超时时间，设置对应的 timeout 参数到对应的值即可。

#### XML 方式

如果使用 XML 的方式引用服务，设置 `<sofa:binding.bolt>` 标签下的 `<sofa:global-attrs>` 标签的 `timeout` 属性的值即可：

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="2000"/>
    </sofa:binding.bolt>
</sofa:reference>
```

#### Annotation 方式

如果使用 Annotation 引用服务，设置 `@SofaReferenceBinding` 的 `timeout` 属性的值即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", timeout = 2000))
private SampleService sampleService;
```

#### Spring 环境 API 方式

如果在 Spring 或者 Spring Boot 的环境下引用服务，设置 `BoltBindingParam` 的 `timeout` 属性的值即可：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setTimeout(2000)
```

#### 非 Spring 环境下 API 方式

如果在非 Spring 环境下直接使用 SOFARPC 的裸 API 引用服务，设置 `ConsumerConfig` 的 `timeout` 属性即可：

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setTimeout(2000);
```

### 方法维度

如果想要单独调整一个服务中某一个方法的超时时间，可以通过在方法维度上设置超时时间来实现。

对于某一个方法来说，优先方法维度的超时时间，如果没有设置，则使用服务维度的超时时间。

#### XML 方式

如果使用 XML 的方式引用一个服务，设置对应的 `<sofa:method>` 标签的 `timeout` 属性即可：

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:method name="hello" timeout="2000"/>
    </sofa:binding.bolt>
</sofa:reference>
```

#### Annotation 方式

目前暂未提供通过 Annotation 的方式来设置方法级别的超时时间。

#### Spring 环境 API 方式

如果在 Spring 或者 Spring Boot 的环境下引用服务，设置 `RpcBindingMethodInfo` 的 `timeout` 属性的值即可：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();

RpcBindingMethodInfo rpcBindingMethodInfo = new RpcBindingMethodInfo();
rpcBindingMethodInfo.setName("hello");
rpcBindingMethodInfo.setTimeout(2000);

List<RpcBindingMethodInfo> rpcBindingMethodInfos = new ArrayList<>();
rpcBindingMethodInfos.add(rpcBindingMethodInfo);

boltBindingParam.setMethodInfos(rpcBindingMethodInfos);
```

#### 非 Spring 环境 API 方式

如果在非 Spring 环境下使用 SOFARPC 的裸 API 引用服务，设置 `MethodConfig` 的 `timeout` 属性即可：

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