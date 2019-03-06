## RESTful 协议基本使用

在 SOFARPC 中，使用不同的通信协议即使用不同的 Binding 即可，如果需要使用 RESTful 协议，只要将 Binding 设置为 REST 即可。

### 发布服务

在定义 RESTful 的服务接口的时候，需要采用 [JAXRS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) 标准的注解在接口上加上元信息，比如下面的接口：

```java
@Path("sample")
public interface SampleService {
    @GET
    @Path("hello")
    String hello();
}
```

> JAXRS 的标准的注解的使用方式可以参考 [RESTEasy 的文档](http://docs.jboss.org/resteasy/docs/3.0.12.Final/userguide/html/Using_Path.html)。

在定义好了接口之后，将接口的实现发布成一个服务，比如，通过 Annotation 的方式：

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

如果要通过其他的方式发布服务，请参考 [Bolt 协议基本使用](./Bolt-Usage.md)。

### 通过浏览器访问服务

在发布服务之后，用户可以通过浏览器来直接访问服务，对于上面的服务，访问的地址如下：

```
http://localhost:8341/sample/hello
```

SOFARPC 的 RESTful 服务的默认端口为 8341。

### 引用服务

除了通过浏览器访问 SOFARPC 发布的 RESTful 服务之外，用户也可以通过 SOFARPC 标准的服务引用的方式来引用服务，比如通过 Annotation 的方式：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "rest"))
private SampleService sampleService;
```

如果要使用其他的方式引用服务，请参考 [Bolt 协议基本使用](./Bolt-Usage.md)。
