# SOFABoot

## Use annotation for service publishing/reference

In addition to the regular xml mode, it is also supported to publish and reference services with annotation in the SOFABoot environment. Similar to xml, we provide
`@SofaService` and `@SofaReference` as well as `@SofaServiceBinding` and `@SofaReferenceBinding` annotation for multi-protocol.

### Service publishing

To publish an RPC service, you only need to add a `@SofaService` annotation on the bean to specify the interface and protocol type.


```java
@SofaService(interfaceType = AnnotationService.class, bindings = { @SofaServiceBinding(bindingType = "bolt") })
@Component
public class AnnotationServiceImpl implements AnnotationService {
    @Override
    public String sayAnnotation(String stirng) {
        return stirng;
    }
}
```


### Service reference

For a bean that needs to reference a remote service, you only need to add the Reference annotation on the attribute or method. This supports the bolt, dubbo, rest protocol.

```java
@Component
public class AnnotationClientImpl {

    @SofaReference(interfaceType = AnnotationService.class, binding = @SofaReferenceBinding(bindingType = "bolt"))
    private AnnotationService annotationService;

    public String sayClientAnnotation(String str) {

        String result = annotationService.sayAnnotation(str);

        return result;
    }
}

```

### Use the demo

You can test in the annotation subproject of the sample project.