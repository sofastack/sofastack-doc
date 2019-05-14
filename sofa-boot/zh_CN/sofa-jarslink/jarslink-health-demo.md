> [工程地址](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/biz-health-check-sample)

## 简介
SOFABoot 扩展了 Spring Boot 的健康检查，详情请移步[SOFABoot 文档](../HealthCheck) 。本样例工程意在演示在合并部署时，如何集成 SOFABoot 健康检查组件。合并部署时的健康检查和单个 SOFABoot 应用的健康检查存在如下区别：
+ 静态合并部署时，Ark 包正常启动的前提必须是所有 Biz 都健康检查通过。
+ 使用 Jarslink2.0 运行时动态部署 Biz 时，只有健康检查通过才会部署成功。
+ 合并部署时，访问 Spring Boot 默认的 /health 会新增名为 multiApplicationHealthChecker 的检查项，用于检查所有的 Biz 健康状态，只有所有的 Biz 健康检查成功才认为健康检查通过。

## 依赖
合并部署时，为了集成 SOFABoot 健康检查能力，需要添加如下依赖：
```xml
<!--health check-->
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
    <classifier>ark-plugin</classifier>
</dependency>

<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>healthcheck-sofa-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**请注意，在引入 healthcheck-sofa-boot-starter 依赖是，排除了 spring-boot-starter-web，避免启动多个 Web 应用**

## 演示
+ cd biz-health-check-sample/app-one && mvn clean package
在 app-one 应用根目录中执行 mvn clean package 命令，将应用打包成 Ark 包和 Biz 包，文件将输出到 biz-health-check-sample/app-one/target 目录

+ cd biz-health-check-sample/app-two && mvn clean package
在 app-two 应用根目录中执行 mvn clean package 命令，将应用打包成 Ark 包和 Biz 包，文件将输出到 biz-health-check-sample/app-two/target 目录

+ 使用 java -jar 启动 app-one 应用的 Ark 包

+ 启动完成之后，浏览器访问 http://localhost:8080/health ，这是 Spring Boot 默认的健康检查 endpoint，检查结果会新增名为 multiApplicationHealthChecker 的检查项，此时只有一个 Biz，页面显示如下：
```json
{"status":"UP","sofaBootComponentHealthCheckInfo":{"status":"UP","Middleware":{"RUNTIME-COMPONENT":{"status":"UP"}}},"springContextHealthCheckInfo":{"status":"UP"},"multiApplicationHealthChecker":{"status":"UP","Biz: app-one:1.0.0 health check":"passed"},"diskSpace":{"status":"UP","total":249769230336,"free":124531359744,"threshold":10485760}}
```

+ 使用 telnet localhost 1234 进入 Jarslink2.0 指令交互界面，并执行 install -b 指令，安装启动 app-two 的 Biz 包。

+ 重新再浏览器访问 http://localhost:8080/health ，会发现在 multiApplicationHealthChecker 检查项中，会显示 app-two 的健康检查结果，页面显示如下：
```json
{"status":"UP","sofaBootComponentHealthCheckInfo":{"status":"UP","Middleware":{"RUNTIME-COMPONENT":{"status":"UP"}}},"springContextHealthCheckInfo":{"status":"UP"},"multiApplicationHealthChecker":{"status":"UP","Biz: app-one:1.0.0 health check":"passed","Biz: app-two:1.0.0 health check":"passed"},"diskSpace":{"status":"UP","total":249769230336,"free":124521283584,"threshold":10485760}}
```