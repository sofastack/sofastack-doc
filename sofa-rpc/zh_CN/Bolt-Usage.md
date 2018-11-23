## Bolt 协议基本使用

### 发布服务

使用 SOFARPC 发布一个 Bolt 协议的服务，只需要增加名称为 Bolt 的 Binding 即可，不同的使用方式添加 Bolt Binding 的方式如下：

#### XML

使用 XML 发布一个 Bolt 协议只需要在 `<sofa:service>` 标签下增加 `<sofa:binding.bolt>` 标签即可：

```xml
<sofa:service ref="sampleService" interface="com.alipay.sofa.rpc.sample.SampleService">
    <sofa:binding.bolt/>
</sofa:service>
```

#### Annotation

使用 Annotation 发布一个 Bolt 协议的服务只需要设置 `@SofaServiceBinding` 的 `bindingType` 为 `bolt` 即可：

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "bolt")})
public class SampleServiceImpl implements SampleService {
}
```

#### Spring 环境下 API 方式

在 Spring 或者 Spring Boot 环境下发布一个 Bolt 协议的服务只需要往 `ServiceParam` 里面增加一个 `BoltBindingParam` 即可：

```java
ServiceParam serviceParam = new ServiceParam();
serviceParam.setInterfaceType(SampleService.class); // 设置服务接口
serviceParam.setInstance(new SampleServiceImpl()); // 设置服务接口的实现
            
List<BindingParam> params = new ArrayList<BindingParam>();
BindingParam serviceBindingParam = new BoltBindingParam();
params.add(serviceBindingParam);
serviceParam.setBindingParams(params);
```

#### 非 Spring 环境下的 API 方式

在非 Spring 环境下使用 SOFARPC 的裸 API 提供 Bolt 协议的服务，只需要将 Protocol 为 Bolt 的 `ServerConfig` 设置给对应的 `ProviderConfig`：

```java
RegistryConfig registryConfig = new RegistryConfig()
        .setProtocol("zookeeper")
        .setAddress("127.0.0.1:2181");
// 新建一个协议为 Bolt 的 ServerConfig
ServerConfig serverConfig = new ServerConfig()
        .setPort(8803)
        .setProtocol("bolt");
ProviderConfig<SampleService> providerConfig = new ProviderConfig<SampleService>()
        .setInterfaceId(SampleService.class.getName())
        .setRef(new SampleServiceImpl())
        .setServer(serverConfig) // 将 ServerConfig 设置给 ProviderConfig，表示这个服务发布的协议为 Bolt。
        .setRegistry(registryConfig);
providerConfig.export();
```

### 引用服务

使用 SOFARPC 引用一个 Bolt 服务，只需要增加名称为 Bolt 的 Binding 即可，不同的使用方式添加 Bolt Binding 的方式如下：

#### XML

使用 XML 引用一个 Bolt 协议的服务只需要在 `<sofa:reference>` 标签下增加 `<sofa:binding.bolt>` 标签即可：

```xml
<sofa:reference id="sampleService" interface="com.alipay.sofa.rpc.sample.SampleService">
    <sofa:binding.bolt/>
</sofa:reference>
```

#### Annotation

使用 Annotation 引用一个 Bolt 协议的服务只需要设置 `@SofaReferenceBinding` 的 `bindingType` 为 `bolt` 即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt"))
private SampleService sampleService;
```

#### Spring 环境下 API 方式

在 Spring 或者 Spring Boot 环境下引用一个 Bolt 协议的服务只需要往 `ReferenceParam` 里面增加一个 `BoltBindingParam` 即可：

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);
ReferenceParam<SampleService> referenceParam = new ReferenceParam<SampleService>();
referenceParam.setInterfaceType(SampleService.class);

BindingParam refBindingParam = new BoltBindingParam();
referenceParam.setBindingParam(refBindingParam);
```

#### 非 Spring 环境下的 API 方式

在非 Spring 环境下使用 SOFARPC 的裸 API 引用一个 Bolt 协议的服务，只需要设置 `ConsumerConfig` 的 Protocol 为 `bolt` 即可：

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt");
SampleService sampleService = consumerConfig.refer();
```