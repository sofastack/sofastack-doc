## Basic usage of Dubbo protocol

In SOFARPC, to use different communication protocols, it is only required to use different Bindings. If you need to use the Dubbo protocol, just set Binding to Dubbo. The following shows an example using Annotation. For other usage methods, refer to [Basic usage of Bolt protocol](./Bolt-Usage.md).

### Publish Service

To publish a Dubbo service, just set the `bindingType` of `@SofaServiceBinding` to `dubbo`:

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "dubbo")})
public class SampleServiceImpl implements SampleService {
}
```

### Reference Service

To reference a Dubbo service, just set the `bindingType` of `@SofaReferenceBinding` to `dubbo`:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "dubbo"), jvmFirst = false)
private SampleService sampleService;
```

### Set the Group of Dubbo Service

In the SOFARPC program model, there is no field called Group, but there is a model of `uniqueId`, which can be directly mapped to the Group in Dubbo model. For example, the following code is to publish a service whose Group is `groupDemo`:

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "dubbo")}, uniqueId = "groupDemo")
public class SampleServiceImpl implements SampleService {
}
```

The following code is to reference a service whose Group is `groupDemo`:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "dubbo"), uniqueId = "groupDemo", jvmFirst = false)
private SampleService sampleService;
```

> Note that Dubbo protocol currently only supports Zookeeper as service registry center.