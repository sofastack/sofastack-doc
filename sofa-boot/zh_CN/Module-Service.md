SOFABoot 提供三种方式给开发人员发布和引用 JVM 服务

- XML 方式
- Annotation 方式
- 编程 API 方式

### XML 方式

#### 服务发布

首先需要定义一个 Bean：

```xml
<bean id="sampleService" class="com.alipay.sofa.runtime.test.service.SampleServiceImpl">
```

然后通过 SOFA 提供的 Spring 扩展标签来将上面的 Bean 发布成一个 SOFA JVM 服务。

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService">
	<sofa:binding.jvm/>
</sofa:service>
```

上面的配置中的 interface 指的是需要发布成服务的接口，ref 指向的是需要发布成 JVM 服务的 Bean，至此，我们就已经完成了一个 JVM 服务的发布。

#### 服务引用

使用 SOFA 提供的 Spring 扩展标签引用服务: 

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleServiceRef">
	<sofa:binding.jvm/>
</sofa:reference>
```

上面的配置中的 interface 是服务的接口，需要和发布服务时配置的 interface 一致。id 属性的含义同 Spring BeanId。上面的配置会生成一个 id 为 sampleServiceRef 的 Spring Bean，你可以将 sampleServiceRef 这个 Bean 注入到当前 SOFABoot 模块 Spring 上下文的任意地方。

> service/reference 标签还支持 RPC 服务发布，相关文档: [RPC 服务发布与引用](https://github.com/sofastack/sofa-rpc/wiki/Publish-And-Reference)

### Annotation 方式

> **警告**
>
> 如果一个服务已经被加上了 @SofaService 的注解，它就不能再用 XML 的方式去发布服务了，选择一种方式发布服务，而不是两种混用。

除了通过 XML 方式发布 JVM 服务和引用之外，SOFABoot 还提供了 Annotation 的方式来发布和引用 JVM 服务。通过 Annotation 方式发布 JVM 服务，只需要在实现类上加一个 @SofaService 注解即可，如下：

```java
@SofaService
public class SampleImpl implements SampleInterface {
   public void test() {

   }
}
```

> **提示**
>
> @SofaService 的作用是将一个 Bean 发布成一个 JVM 服务，这意味着虽然你可以不用再写 \<sofa:service/\> 的配置，但是还是需要事先将 @SofaService 所注解的类配置成一个 Spring Bean。

在使用 XML 配置 \<sofa:service/\> 的时候，我们配置了一个 interface 属性，但是在使用 @SofaService 注解的时候，却没有看到有配置服务接口的地方。这是因为当被 @SofaService 注解的类只有一个接口的时候，框架会直接采用这个接口作为服务的接口。当被 @SofaService 注解的类实现了多个接口时，可以设置 @SofaService 的 interfaceType 字段来指定服务接口，比如下面这样：

```java
@SofaService(interfaceType=SampleInterface.class)
public class SampleImpl implements SampleInterface, Serializable {
   public void test() {

   }
}
```

和 @SofaService 对应，Sofa 提供了 @SofaReference 来引用一个 JVM 服务。假设我们需要在一个 Spring Bean 中使用 SampleJvmService 这个 JVM 服务，那么只需要在字段上加上一个 @SofaReference 的注解即可：

```java
public class SampleServiceRef {
    @SofaReference
    private SampleService sampleService;
}
```

和 @SofaService 类似，我们也没有在 @SofaReference 上指定服务接口，这是因为 @SofaReference 在不指定服务接口的时候，会采用被注解字段的类型作为服务接口，你也可以通过设定 @SofaReference 的 interfaceType 属性来指定：

```java
public class SampleServiceRef {
    @SofaReference(interfaceType=SampleService.class)
    private SampleService sampleService;
}
```

使用 @SofaService 注解发布服务时，需要在实现类上打上 @SofaService 注解；在 Spring Boot 使用 Bean Method 创建 Bean 时，会导致 @Bean 和 @SofaService 分散在两处，而且无法对同一个实现类使用不同的 unique id。因此自 SOFABoot v2.6.0 及 v3.1.0 版本起，支持 @SofaService 作用在 Bean Method 之上，例如：
```java
@Configuration
public class SampleSofaServiceConfiguration {
    @Bean("sampleSofaService")
    @SofaService(uniqueId = "service1")
    SampleService service() {
        return new SampleServiceImpl("");
    }
}
```

同样为了方便在 Spring Boot Bean Method 使用注解 @SofaReference 引用服务，自 SOFABoot v2.6.0 及 v3.1.0 版本起，支持在 Bean Method 参数上使用 @SofaReference 注解引用 JVM 服务，例如：
```java
@Configuration
public class MultiSofaReferenceConfiguration {
    @Bean("sampleReference")
    TestService service(@Value("$spring.application.name") String appName,
                        @SofaReference(uniqueId = "service") SampleService service) {
        return new TestService(service);
    }
}
```

### 编程 API 方式

SOFABoot 为 JVM 服务的发布和引用提供了一套编程 API 方式，方便直接在代码中发布和引用 JVM 服务，与 Spring 的 ApplicationContextAware 类似，为使用编程 API 方式，首先需要实现 ClientFactoryAware 接口获取编程组件 API：

```java
public class ClientFactoryBean implements ClientFactoryAware {
    private ClientFactory clientFactory;

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

以 SampleService 为例，看下如何使用 clientFactory 通过编程 API 方式发布 JVM 服务：

```java
ServiceClient serviceClient = clientFactory.getClient(ServiceClient.class);

ServiceParam serviceParam = new ServiceParam();
serviceParam.setInstance(new SampleServiceImpl());
serviceParam.setInterfaceType(SampleService.class);
serviceClient.service(serviceParam);
```

上面的代码中

1.  首先通过 clientFactory 获得 ServiceClient 对象
2.  然后构造 ServiceParam 对象，ServiceParam 对象包含发布服务所需参数，通过 setInstance 方法来设置需要被发布成 JVM 服务的对象，setInterfaceType 来设置服务的接口
3.  最后，调用 ServiceClient 的 service 方法，发布一个 JVM 服务

通过编程 API 方式引用 JVM 服务的代码也是类似的：

```java
ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);

ReferenceParam<SampleService> referenceParam = new ReferenceParam<SampleService>();
referenceParam.setInterfaceType(SampleService.class);
SampleService proxy = referenceClient.reference(referenceParam);
```

同样，引用一个 JVM 服务只需从 ClientFactory 中获取一个 ReferenceClient ，然后和发布一个服务类似，构造出一个 ReferenceParam，然后设置好服务的接口，最后调用 ReferenceClient 的 reference 方法即可。

> **提示**
> 
> 通过动态客户端创建的 Reference 对象是一个非常重的对象，请大家在使用的时候不要频繁创建，自行做好缓存，否则可能存在内存溢出的风险。

除了实现 ClientFactoryAware 接口用于获取 ServiceClient 和 ReferenceClient 对象，还可以使用简便的注解 `@SofaClientFactory` 获取编程 API，例如

```java
public class ClientBean {
    @SofaClientFactory
    private ReferenceClient referenceClient;
    
    @SofaClientFactory
    private ServiceClient serviceClient;
}
```

### uniqueId

有些时候，针对一个接口，我们会需要发布两个服务出来，分别对应到不同的实现。继续前面的 sampleService 的例子，我们可能有两个 SampleService 的实现，这两个实现我们都需要发布成 SOFA 的 JVM Service，按照前面的教程，采用 XML 的方式，我们就可能用下面这种方式进行配置：

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService1">
</sofa:service>
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService2">
</sofa:service>
```

上面的服务发布没有什么问题，但是当需要引用服务的时候，就出现了问题了，例如我们使用以下配置：

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService">
</sofa:reference>
```

这个 JVM 引用到底引用的是哪个 JVM 服务呢，我们无从知晓。

为了解决上面的这种问题，SOFABoot 引入了 uniqueId 的概念，针对服务接口一样的 JVM 服务，可以通过 uniqueId 来进行区分，上面的服务发布的代码我们加入 uniqueId 后，我们可以改成下面这样：

```xml
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService1" unique-id="ss1">
</sofa:service>
<sofa:service interface="com.alipay.sofa.runtime.test.service.SampleService" ref="sampleService2" unique-id="ss2">
</sofa:service>
```

然后，在引用服务的时候，如果我们要使用 sampleService1 的服务，可以指定 unique-id 为 ss1，比如：

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService" unique-id="ss1">
</sofa:reference>
```

如果要使用 sampleService2 的服务，可以指定 unique-id 为 ss2，比如：

```xml
<sofa:reference interface="com.alipay.sofa.runtime.test.service.SampleService" id="sampleService" unique-id="ss2">
</sofa:reference>
```

上面说的是在 XML 的方式中使用 uniqueId。当你用 Annotation 的方式发布 JVM 服务和引用的时候，可以通过设置 @SofaService 和 @SofaReference 的 uniqueId 属性来设置 uniqueId。当你用编程 API 的方式发布或者引用 JVM 服务的时候，可以通过 ServiceParam 和 ReferenceParam 的 setUniqueId 方法来设置 uniqueId。 
