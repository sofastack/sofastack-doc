在本文档中，将创建一个 Spring Boot 的工程，引入 SOFABoot 基础依赖，并且引入 SOFABoot 的健康检查扩展能力，演示如何快速上手 SOFABoot。

## 环境准备

要使用 SOFABoot，需要先准备好基础环境，SOFABoot 依赖以下环境：
- JDK7 或 JDK8 
- 需要采用 Apache Maven 3.2.5 或者以上的版本来编译

## 创建工程

SOFABoot 是直接构建在 Spring Boot 之上，因此可以使用 [Spring Boot 的工程生成工具](http://start.spring.io/) 来生成，在本文档中，我们需要添加一个 Web 的依赖，以便最后在浏览器中查看效果。

## 引入 SOFABoot 

在创建好一个 Spring Boot 的工程之后，接下来就需要引入 SOFABoot 的依赖，首先，需要将上文中生成的 Spring Boot 工程的 `zip` 包解压后，修改 maven 项目的配置文件 `pom.xml`，将

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/> 
</parent>
```

替换为：

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofa.boot.version}</version>
</parent>
```
这里的 `${sofa.boot.version}` 指定具体的 SOFABoot 版本，参考[发布历史](https://github.com/sofastack/sofa-boot/releases)。 然后，添加 SOFABoot 健康检查扩展能力的依赖及 Web 依赖(方便查看健康检查结果)：
```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>healthcheck-sofa-boot-starter</artifactId>
</dependency>

<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

最后，在工程的 `application.properties` 文件下添加 SOFABoot 工程常用的参数配置，其中 `spring.application.name` 是必需的参数，用于标示当前应用的名称；`logging path` 用于指定日志的输出目录。

```
# Application Name
spring.application.name=SOFABoot Demo
# logging path
logging.path=./logs
```

## 运行

可以将工程导入到 IDE 中运行生成的工程里面中的 `main` 方法（一般上在 XXXApplication 这个类中）启动应用，也可以直接在该工程的根目录下运行 `mvn spring-boot:run`，将会在控制台中看到启动打印的日志：

```
2018-04-05 21:36:26.572  INFO ---- Initializing ProtocolHandler ["http-nio-8080"]
2018-04-05 21:36:26.587  INFO ---- Starting ProtocolHandler [http-nio-8080]
2018-04-05 21:36:26.608  INFO ---- Using a shared selector for servlet write/read
2018-04-05 21:36:26.659  INFO ---- Tomcat started on port(s): 8080 (http)
```

可以通过在浏览器中输入 [http://localhost:8080/sofaboot/versions](http://localhost:8080/sofaboot/versions) 来查看当前 SOFABoot 中使用 Maven 插件生成的版本信息汇总，结果类似如下：

```json
[
  {
    GroupId: "com.alipay.sofa",
    Doc-Url: "https://github.com/sofastack/sofa-boot",
    ArtifactId: "infra-sofa-boot-starter",
    Built-Time: "2018-04-05T20:55:26+0800",
    Commit-Time: "2018-04-05T20:54:26+0800",
    Commit-Id: "049bf890bb468aafe6a3e07b77df45c831076996",
    Version: "2.4.4"
  }
]
```
**注: 在 SOFABoot 3.x 中调整了 endpoint 路径，sofaboot/versions 更改为 actuator/versions**

可以通过在浏览器中输入 [http://localhost:8080/health/readiness](http://localhost:8080/health/readiness) 查看应用 Readiness Check 的状况，类似如下：

```json
{
  status: "UP",
  sofaBootComponentHealthCheckInfo: {
    status: "UP"
  },
  springContextHealthCheckInfo: {
    status: "UP"
  },
  DiskSpace: {
    status: "UP",
    total: 250140434432,
    free: 22845308928,
    threshold: 10485760
  }
}
```
**注: 在 SOFABoot 3.x 中调整了 endpoint 路径，health/readiness 更改为 actuator/readiness**

`status: "UP"` 表示应用 Readiness Check 健康的。可以通过在浏览器中输入 `http://localhost:8080/health` 来查看应用的运行时健康状态（可能会随着时间发生变化）。  

**注: 在 SOFABOOT 3.X 中调整了 endpoint 路径，/health 更改为 /actuator/health**

## 查看日志

在上面的 `application.properties` 里面，我们配置的日志打印目录是 `./logs` 即当前应用的根目录（我们可以根据自己的实践需要配置），在当前工程的根目录下可以看到类似如下结构的日志文件：

```
./logs
├── health-check
│   ├── sofaboot-common-default.log
│   └── sofaboot-common-error.log
├── infra
│   ├── common-default.log
│   └── common-error.log
└── spring.log
```

如果应用启动失败或者健康检查返回失败，可以通过相应的日志文件找到错误的原因，有些需要关注 `common-error.log` 日志。

附此示例工程的[源代码地址](https://github.com/sofastack/sofa-boot/tree/master/sofaboot-samples/sofaboot-sample)。

## 测试
我们知道，SpringBoot 官方提供了和 JUnit4 集成的 `SpringRunner`, 用于集成测试用例的编写； 在 SOFABoot 中，依然可以使用原生的 `SpringRunner`， 但是推荐使用 SOFABoot 自带的 `SofaBootRunner` 以及 `SofaJUnit4Runner` 编写集成测试和单元测试；应用需要额外引入如下 Starter:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>test-sofa-boot-starter</artifactId>
</dependency>
```

需要注意的是，如果需要使用 SOFABoot 的[类隔离](./Classloader-Isolation)的能力，则必须需要引入上述的依赖，并且使用 `SofaBootRunner` 和 `SofaJUnit4Runner`  来测试。

## 模块化开发

这部分将演示如何在 SOFABoot 环境下进行模块化开发，您可以直接在工程下找到本文档的[示例代码](https://github.com/sofastack/sofa-boot/tree/master/sofaboot-samples/sofaboot-sample-with-isle)，项目一共包括四个模块：

```text
.
│
├── service-facade 
│ 
├── service-provider
│ 
├── service-consumer
│ 
└── sofa-boot-run
```

各个模块的作用如下：

- service-facade: 演示 JVM 服务发布与引用的 API 包；
- service-provider: 演示 XML 方式、Annotation 方式、API 方式发布 JVM 服务；
- service-consumer: 演示 XML 方式、Annotation 方式、API 方式引用 JVM 服务；
- sofa-boot-run: 启动包含 SOFABoot 模块的 SOFA Boot 应用。

**建议在学习该 Demo 之前，参考模块[隔离文档](./Modular-Development)**

### 定义服务 API

service-facade 模块包含用于演示 JVM 服务发布与引用的 API :

```java
public interface SampleJvmService {
    String message();
}
```

### 发布 JVM 服务

service-provider 是一个 SOFABoot 模块，用于演示 XML 方式、Annotation 方式、API 方式发布 JVM 服务。

#### 定义 SOFABoot 模块

为 service-provider 模块增加 sofa-module.properties 文件，将其定义为 SOFABoot 模块:

```properties
Module-Name=com.alipay.sofa.service-provider
```

#### XML 方式发布服务

实现 SampleJvmService 接口:

```java
public class SampleJvmServiceImpl implements SampleJvmService {
    private String message;

    @Override
    public String message() {
        System.out.println(message);
        return message;
    }

    // getters and setters
}
```

增加 META-INF/spring/service-provide.xml 文件，将 SampleJvmServiceImpl 发布为 JVM 服务:

```xml
<bean id="sampleJvmService" class="com.alipay.sofa.isle.sample.SampleJvmServiceImpl">
    <property name="message" value="Hello, jvm service xml implementation."/>
</bean>

<sofa:service ref="sampleJvmService" interface="com.alipay.sofa.isle.sample.SampleJvmService">
    <sofa:binding.jvm/>
</sofa:service>
```

#### Annotation 方式发布服务

实现 SampleJvmService 接口并增加 @SofaService 注解:

```java
@SofaService(uniqueId = "annotationImpl")
public class SampleJvmServiceAnnotationImpl implements SampleJvmService {
    @Override
    public String message() {
        String message = "Hello, jvm service annotation implementation.";
        System.out.println(message);
        return message;
    }
}
```

为了区分 XML 方式发布的 JVM 服务，注解上需要增加 uniqueId 属性。

将 SampleJvmServiceAnnotationImpl 配置成一个 Spring Bean，保证 @SofaService 注解生效:

```xml
<bean id="sampleJvmServiceAnnotation" class="com.alipay.sofa.isle.sample.SampleJvmServiceAnnotationImpl"/>
```

#### API 方式发布服务

增加 PublishServiceWithClient 类，演示 API 方式发布服务:

```java
public class PublishServiceWithClient implements ClientFactoryAware {
    private ClientFactory clientFactory;

    public void init() {
        ServiceClient serviceClient = clientFactory.getClient(ServiceClient.class);
        ServiceParam serviceParam = new ServiceParam();
        serviceParam.setInstance(new SampleJvmServiceImpl(
            "Hello, jvm service service client implementation."));
        serviceParam.setInterfaceType(SampleService.class);
        serviceParam.setUniqueId("serviceClientImpl");
        serviceClient.service(serviceParam);
    }

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

将 PublishServiceWithClient 配置为 Spring Bean，并设置 init-method ，使PublishServiceWithClient 在 Spring 刷新时发布服务:

```xml
<bean id="publishServiceWithClient" class="com.alipay.sofa.isle.sample.PublishServiceWithClient" init-method="init"/>
```

### 引用 JVM 服务

service-consumer 是一个 SOFABoot 模块，用于演示 XML 方式、Annotation 方式、API 方式引用 JVM 服务。

#### 定义 SOFABoot 模块

为 service-consumer 模块增加 sofa-module.properties 文件，将其定义为 SOFABoot 模块:

```properties
Module-Name=com.alipay.sofa.service-consumer
Require-Module=com.alipay.sofa.service-provider
```

在 sofa-module.properties 文件中需要指定 Require-Module，保证 service-provider 模块在 service-consumer 模块之前刷新。

#### XML 方式引用服务

增加 META-INF/spring/service-consumer.xml 文件，引用 service-provider 模块发布的服务:

```xml
<sofa:reference id="sampleJvmService" interface="com.alipay.sofa.isle.sample.SampleJvmService">
    <sofa:binding.jvm/>
</sofa:service>
```

#### Annotation 方式引用服务

定义 JvmServiceConsumer 类，并在其 sampleJvmServiceAnnotationImpl 属性上增加 @SofaReference 注解: 

```java
public class JvmServiceConsumer implements ClientFactoryAware {
    @SofaReference(uniqueId = "annotationImpl")
    private SampleJvmService sampleJvmServiceAnnotationImpl;
}
```

将 JvmServiceConsumer 配置成一个 Spring Bean，保证 @SofaReference 注解生效:

```xml
<bean id="consumer" class="com.alipay.sofa.isle.sample.JvmServiceConsumer" init-method="init" />
```

#### API 方式引用服务

JvmServiceConsumer 实现 ClientFactoryAware 接口，并在其 init 方法中引用 JVM 服务:

```java
public class JvmServiceConsumer implements ClientFactoryAware {
    private ClientFactory    clientFactory;

    public void init() {
        ReferenceClient referenceClient = clientFactory.getClient(ReferenceClient.class);
        ReferenceParam<SampleJvmService> referenceParam = new ReferenceParam<>();
        referenceParam.setInterfaceType(SampleJvmService.class);
        referenceParam.setUniqueId("serviceClientImpl");
        SampleJvmService sampleJvmServiceClientImpl = referenceClient.reference(referenceParam);
        sampleJvmServiceClientImpl.message();
    }

    @Override
    public void setClientFactory(ClientFactory clientFactory) {
        this.clientFactory = clientFactory;
    }
}
```

### 启动 SOFABoot 应用

将模块 parent 配置为 SOFABoot:

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>2.4.4</version>
</parent>
```

为模块增加 isle-sofa-boot-starter 及 service-provider 、 service-consumer 依赖:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>isle-sofa-boot-starter</artifactId>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>service-provider</artifactId>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>service-consumer</artifactId>
</dependency>
```

启动 ApplicationRun 类，控制台将看到以下输出:

```text
Hello, jvm service xml implementation.
Hello, jvm service annotation implementation.
Hello, jvm service service client implementation.
```

### 编写测试用例

SOFABoot 模块化测试方法与 Spring Boot 测试方法一致，只需在测试用例上增加 @SpringBootTest 注解及 @RunWith(SpringRunner.class) 注解即可。在测试用例中，还可以使用 @SofaReference 注解，对 SOFABoot 模块发布的服务进行测试：

```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class SofaBootWithModulesTest {
    @SofaReference
    private SampleJvmService sampleJvmService;

    @SofaReference(uniqueId = "annotationImpl")
    private SampleJvmService sampleJvmServiceAnnotationImpl;

    @SofaReference(uniqueId = "serviceClientImpl")
    private SampleJvmService sampleJvmServiceClientImpl;

    @Test
    public void test() {
        Assert.assertEquals("Hello, jvm service xml implementation.", sampleJvmService.message());
        Assert.assertEquals("Hello, jvm service annotation implementation.",
            sampleJvmServiceAnnotationImpl.message());
        Assert.assertEquals("Hello, jvm service service client implementation.",
            sampleJvmServiceClientImpl.message());
    }
}
```
