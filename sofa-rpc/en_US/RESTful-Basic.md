## Basic usage of RESTful protocol

In SOFARPC, using different communication protocols is equal to using different Bindings. If you need to use the RESTful protocol, just set Binding to REST.

### Publish Service

When defining a RESTful service interface, you need to add meta information to the interface using the annotations in [JAXRS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) standard, such as the following interface:

```java
@Path("sample")
public interface SampleService {
    @GET
    @Path("hello")
    String hello();
}
```

> The annotations in JAXRS standard can be found in [RESTEasy documentation](http://docs.jboss.org/resteasy/docs/3.0.12.Final/userguide/html/Using_Path.html).

After the interface is defined, you can publish the implementation of the interface as a service, for example, by means of Annotation:

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "rest")})
public class RestfulSampleServiceImpl implements SampleService {
    @Override
    public String hello() {
        return "Hello";
    }
}
```

If you want to publish the service by other methods, please refer to [Basic usage of Bolt protocol](./Bolt-Usage.md).

### Access services through browser

After the service is published, you can directly access the service through the browser. For the above service, the access address is as follows:

```
http://localhost:8341/sample/hello
```

The default port for SOFARPC RESTful service is 8341.

### Reference Service

In addition to accessing RESTful services published by SOFARPC through a browser, you can also reference services through the standard SOFARPC service reference methods, such as Annotation:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "rest"))
private SampleService sampleService;
```

If you want to reference the service by other methods, please refer to [Basic usage of Bolt protocol](./Bolt-Usage.md).
