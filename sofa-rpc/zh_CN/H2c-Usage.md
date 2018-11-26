## H2C 协议基本使用

在 SOFARPC 中，使用不同的通信协议只要设置使用不同的 Binding 即可，如果需要使用 H2C 协议，只要将 Binding 设置为 H2C 即可。下面使用以注解的方式来例举，其他的使用方式可以参考 [Bolt 协议基本使用](./Bolt-Usage)，这里不再重复说明。：

### 发布服务

发布一个 H2C 的服务，只需要将 `@SofaServiceBinding` 的 `bindingType` 设置为 `h2c` 即可：

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "h2c")})
public class SampleServiceImpl implements SampleService {
}
```

### 引用服务

引用一个 H2C 的服务，只需要将 `@SofaReferenceBinding` 的 `bindingType` 设置为 `h2c` 即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "h2c"), jvmFirst = false)
private SampleService sampleService;
```