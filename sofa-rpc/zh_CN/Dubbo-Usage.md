## Dubbo 协议基本使用

在 SOFARPC 中，使用不同的通信协议只要设置使用不同的 Binding 即可，如果需要使用 Dubbo 协议，只要将 Binding 设置为 Dubbo 即可。下面使用以注解的方式来例举，其他的使用方式可以参考 [Bolt 协议基本使用](./Bolt-Usage.md)，这里不再重复说明。：

### 发布服务

发布一个 Dubbo 的服务，只需要将 `@SofaServiceBinding` 的 `bindingType` 设置为 `dubbo` 即可：

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "dubbo")})
public class SampleServiceImpl implements SampleService {
}
```

### 引用服务

引用一个 Dubbo 的服务，只需要将 `@SofaReferenceBinding` 的 `bindingType` 设置为 `dubbo` 即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "dubbo"), jvmFirst = false)
private SampleService sampleService;
```

### 设置 Dubbo 服务的 Group

在 SOFARPC 的模型中，没有直接的一个字段叫做 Group，但是 SOFARPC 有一个 `uniqueId` 的模型，可以直接映射到 Dubbo 的模型中的 Group，比如下面的代码，就是发布了一个 Group 为 `groupDemo` 的服务：

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "dubbo")}, uniqueId = "groupDemo")
public class SampleServiceImpl implements SampleService {
}
```

如下的代码，就是引用了一个 Group 为 `groupDemo` 的服务：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "dubbo"), uniqueId = "groupDemo", jvmFirst = false)
private SampleService sampleService;
```

> 注意，目前 Dubbo 协议只支持 Zookeeper 作为服务注册中心。