## Basic usage of H2C protocol

In SOFARPC, to use different communication protocols, it is only required to use different Bindings. If you need to use the H2C protocol, just set Binding to H2C. The following shows an example using Annotation. For other usage methods, refer to [Basic usage of Bolt protocol](./Bolt-Usage).

### Publish Service

To publish an H2C service, just set the `bindingType` of `@SofaServiceBinding` to `h2c`:

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "h2c")})
public class SampleServiceImpl implements SampleService {
}
```

### Reference Service

To reference a H2C service, just set the `bindingType` of `@SofaReferenceBinding` to `h2c`:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "h2c"), jvmFirst = false)
private SampleService sampleService;
```