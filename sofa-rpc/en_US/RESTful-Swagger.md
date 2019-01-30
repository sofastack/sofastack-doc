## Integrate SOFARPC RESTful service with Swagger

Currently, SOFARPC does not provide the ability to integrate RESTful service with [Swagger](https://swagger.io/) via one click. The ability will be provided in future versions, but you can refer to this document to integrate RESTful service with Swagger in the existing versions of SOFARPC.

### Introduce Swagger related dependencies

To integrate Swagger, first, you need to introduce Swagger-related dependencies into your application. Since SOFARPC's RESTful protocol is based on the JAXRS standard, we just need to introduce Swagger's JAXRS dependency:

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

As above, the 20.0 version of Guava was introduced to resolve Guava version conflict.

### Add a Swagger RESTful service

To enable Swagger to expose SOFARPC's RESTful services through Swagger OpenAPI, in turn, we can provide Swagger's OpenAPI services through SOFARPC's RESTful services. First, you need to create a new interface:

```java
@Path("swagger")
public interface OpenApiService {
    @GET
    @Path("openapi")
    @Produces("application/json")
    String openApi();
}
```

Then provide an implementation class and publish it as a RESTful service of SOFARPC:

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
        Resources.add(this.getClass().getPackage()); // Scan the package of the current class, or scan the packages of other SOFARPC RESTful service interfaces.
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

In this way, after the application startup, access `http://localhost:8341/swagger/openapi` to get information about all RESTful services published by the current application.

### Solve cross-domain problem

If you launch a Swagger UI on another port and want to access `http://localhost:8341/swagger/openapi` via the Swagger UI to view the API definition and initiate the call, it may be required to resolve the problem of cross-domain access. To solve this problem, you can add the following code before application startup:

```java
import org.jboss.resteasy.plugins.interceptors.CorsFilter;

public static void main(String[] args) {
    CorsFilter corsFilter = new CorsFilter();
    corsFilter.getAllowedOrigins().add("*");
    JAXRSProviderManager.registerCustomProviderInstance(corsFilter);
    SpringApplication.run(DemoApplication.class, args);
}
```

In this way, Swagger UI can access the Swagger OpenAPI provided by the application across domains.