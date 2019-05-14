在 [Ark 服务机制](./ark-service) 中，我们详细介绍了如何引用和发布插件服务，主要是解决 Plugin 和 Biz 的通信问题；为了解决 Biz 之间的通信问题，SOFAArk 引入了 SOFABoot 提供的 `SofaService/SofaReference` 编程界面；下面介绍其使用方法。

### 引入依赖
引入 runtime-sofa-boot-plugin 依赖，如果应用基于 Spring Boot 1.x 开发，推荐使用 v2.6.1 版本；如果应用基于 Spring Boot 2.x 开发，推荐使用 v3.1.3 版本；

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-plugin</artifactId>
    <version>${sofa.boot.version}</version>
</dependency>
```

### 发布和引用 JVM 服务
SOFAArk 引入了 SOFABoot 提供的 `SofaService/SofaReference` JVM 服务概念([参考文档](../Module-Service))，为了方便文档统一，重复其介绍。

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
</sofa:service>
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

### 跳过序列化
在 Biz 之间使用 JVM 服务调用时，因为每个 Biz 有单独的类加载器加载，因此每次 JVM 调用都会走 Hessian 序列化协议；为了性能的提升，有时不希望走序列化，而是走直接调用的方式，此时需要做两步额外的工作，以下面接口服务为例：

```java
public interface SampleService {
    Result service();
}
```

+ 打包插件

因为走直接调用的方式，因此接口类 SampleService 及其依赖（比如参数，返回值）等都需要下沉为 Ark Plugin ，并在插件配置中将这些类导出，这样做的目的是多个 Biz 中使用接口时，该接口类统一委托给 Plugin 加载，否则报错如下：

```text
java.lang.IllegalArgumentException: object is not an instance of declaring class
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at com.alipay.sofa.runtime.integration.invoke.DynamicJvmServiceProxyFinder$DynamicJvmServiceInvoker.doInvoke(DynamicJvmServiceProxyFinder.java:164)
	at com.alipay.sofa.runtime.spi.service.ServiceProxy.invoke(ServiceProxy.java:39)
	at com.alipay.sofa.runtime.service.binding.JvmBindingAdapter$JvmServiceInvoker.doInvoke(JvmBindingAdapter.java:171)
	at com.alipay.sofa.runtime.spi.service.ServiceProxy.invoke(ServiceProxy.java:39)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:212)
	at com.sun.proxy.$Proxy73.service(Unknown Source)
	at com.alipay.sofa.demo.service.SampleRestService.sampleService(SampleRestService.java:87)
```

+ 发布服务指定跳过序列化

默认情况下，通过注解或者xml发布 JVM 服务，在跨 Biz 调用时，都会走序列化，如果想跳过，需要在发布服务时，指定 `serialize = false`

1、注解指定

```java
@SofaService(bindings = {@SofaServiceBinding(serialize = false)})
```

2、xml 指定

```xml
<sofa:binding.jvm serialize="true"/>
```