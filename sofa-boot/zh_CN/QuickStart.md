在本文档中，将创建一个 Spring Boot 的工程，引入 SOFABoot 基础依赖，并且引入 SOFABoot 的健康检查扩展能力，演示如何快速上手 SOFABoot。

## 环境准备

要使用 SOFABoot，需要先准备好基础环境，SOFABoot 依赖以下环境：
- JDK7 或 JDK8 
- 需要采用 Apache Maven 2.2.5 或者以上的版本来编译

## 创建工程

SOFABoot 是直接构建在 Spring Boot 之上，因此可以使用 [Spring Boot 的工程生成工具](http://start.spring.io/) 来生成，在本文档中，我们需要添加一个 Web 的依赖，以便最后在浏览器中查看效果。

## 引入 SOFABoot 

在创建好一个 Spring Boot 的工程之后，接下来就需要引入 SOFABoot 的依赖，首先，需要将上文中生成的 Spring Boot 工程的 `zip` 包解压后，修改 maven 项目的配置文件 `pom.xml`，将

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
    <version>2.3.1</version>
</parent>
```

然后，添加一个 SOFABoot 健康检查扩展能力的依赖：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>healthcheck-sofa-boot-starter</artifactId>
</dependency>
```

最后，在工程的 `application.properties` 文件下添加一个 SOFABoot 必须要使用的参数，包括`spring.application.name` 用于标示当前应用的名称；`logging path` 用于指定日志的输出目录。

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
    Doc-Url: "https://github.com/alipay/sofa-boot",
    ArtifactId: "infra-sofa-boot-starter",
    Built-Time: "2018-04-05T20:55:26+0800",
    Commit-Time: "2018-04-05T20:54:26+0800",
    Commit-Id: "049bf890bb468aafe6a3e07b77df45c831076996",
    Version: "2.3.1"
  }
]
```

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

`status: "UP"` 表示应用 Readiness Check 健康的。可以通过在浏览器中输入 `http://localhost:8080/health` 来查看应用的运行时健康状态（可能会随着时间发生变化）。

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

附此示例工程的[源代码地址](https://github.com/alipay/sofa-boot/tree/master/sofaboot-samples/sofaboot-sample)。

## 测试
我们知道，SpringBoot 官方提供了和 JUnit4 集成的 `SpringRunner`, 用于集成测试用例的编写； 在 SOFABoot 中，依然可以使用原生的 `SpringRunner`， 但是推荐使用 SOFABoot 自带的 `SofaBootRunner` 以及 `SofaJUnit4Runner` 编写集成测试和单元测试；应用需要额外引入如下 Starter:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>test-sofa-boot-starter</artifactId>
</dependency>
```

需要注意的是，如果需要使用 SOFABoot 的[类隔离](./Classloader-Isolation)的能力，则必须需要引入上述的依赖，并且使用 `SofaBootRunner` 和 `SofaJUnit4Runner`  来测试。