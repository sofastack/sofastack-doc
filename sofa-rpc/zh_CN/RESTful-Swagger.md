## 集成 SOFARPC RESTful 服务和 Swagger

目前 SOFARPC 并没有提供 RESTful 服务和 [Swagger](https://swagger.io/) 的一键集成的能力，一键集成的能力将会在后续的版本中提供，但是用户可以参考如下的文档，在现有的 SOFARPC 的版本上将 RESTful 服务和 Swagger 集成在一起。

### 引入 Swagger 相关依赖

要集成 Swagger，首先，需要在应用中引入 Swagger 相关的依赖，由于 SOFARPC 的 RESTful 协议走的是 JAXRS 标准，因此我们引入 Swagger 的 JAXRS 依赖即可：

```xml
<dependency>
    <groupId>io.swagger.core.v3</groupId>
    <artifactId>swagger-jaxrs2</artifactId>
    <version>2.0.0</version>
</dependency>
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>20.0</version>
</dependency>
```

上面引入 Guava 的 20.0 的版本是为了解决 Guava 的版本冲突。

### 增加一个 Swagger 的 RESTful 服务

为了能够让 Swagger 将 SOFARPC 的 RESTful 的服务通过 Swagger OpenAPI 暴露出去，我们可以反过来用 SOFARPC 的 RESTful 的服务提供 Swagger 的 OpenAPI 服务。首先，需要新建一个接口：

```java
@Path("swagger")
public interface OpenApiService {
    @GET
    @Path("openapi")
    @Produces("application/json")
    String openApi();
}
```

然后提供一个实现类，并且发布成 SOFARPC 的 RESTful 的服务：

```java
@Service
@SofaService(bindings = {@SofaServiceBinding(bindingType = "rest")}, interfaceType = OpenApiService.class)
public class OpenApiServiceImpl implements OpenApiService, InitializingBean {
    private OpenAPI openAPI;

    @Override
    public String openApi() {
        return Json.pretty(openAPI);
    }

    @Override
    public void afterPropertiesSet() {
        List<Package> resources = new ArrayList<>();
        resources.add(this.getClass().getPackage()); // 扫描当前类所在的 Package，也可以扫描其他的 SOFARPC RESTful 服务接口所在的 Package
        if (!resources.isEmpty()) {
            // init context
            try {
                SwaggerConfiguration oasConfig = new SwaggerConfiguration()
                        .resourcePackages(resources.stream().map(Package::getName).collect(Collectors.toSet()));

                OpenApiContext oac = new JaxrsOpenApiContextBuilder()
                        .openApiConfiguration(oasConfig)
                        .buildContext(true);
                openAPI = oac.read();
            } catch (OpenApiConfigurationException e) {
                throw new RuntimeException(e.getMessage(), e);
            }
        }
    }
}
```

这样，应用启动后，访问 `http://localhost:8341/swagger/openapi` 即可得到当前的应用发布的所有的 RESTful 的服务的信息。

### 解决跨域问题

如果用户在另外一个端口中启动了一个 Swagger UI，并且希望通过 Swagger UI 来访问 `http://localhost:8341/swagger/openapi` 查看 API 定义，发起调用，那么可能需要解决访问跨域的问题，要解决 SOFARPC RESTful 服务访问跨域的问题，可以在应用启动前增加如下的代码：

```java
import org.jboss.resteasy.plugins.interceptors.CorsFilter;

public static void main(String[] args) {
    CorsFilter corsFilter = new CorsFilter();
    corsFilter.getAllowedOrigins().add("*");
    JAXRSProviderManager.registerCustomProviderInstance(corsFilter);
    SpringApplication.run(DemoApplication.class, args);
}
```

这样，Swagger UI 就可以跨域访问应用提供的 Swagger OpenAPI 了。