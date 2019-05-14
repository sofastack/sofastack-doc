> [工程地址](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/biz-jvm-invocation-sample)

## 简介
在合并部署时，Biz 之间除了可以使用 RPC 框架通信之外，还可以通过发布和引用 JVM 服务进行通信。本样例工程意在演示两个 Biz 如何使用 JVM 服务进行通信。

在 biz-jvm-invocation-sample 内部，有三个子工程，其作用如下：
+ facade: 一个普通的 Java 模块，定义了接口 SampleJvmService:
```java
package me.qlong.tech.service;

public interface SampleJvmService {
    String service();
}
```
+ app-one: 一个 SOFABoot Web 应用，定义了一个简单的 rest 请求，通过注解 `@SofaReference` 引用了接口为 SampleJvmService 的 JVM 服务。当触发页面请求时，会尝试调用该 JVM 服务，关键代码：
```java
package me.qlong.controller;

import com.alipay.sofa.runtime.api.annotation.SofaReference;
import me.qlong.tech.service.SampleJvmService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @SofaReference
    private SampleJvmService sampleJvmService;

    @RequestMapping("/hello")
    public String hello() {
        return sampleJvmService.service();
    }

}
```

+ app-two: 一个 SOFABoot 非 Web 应用，通过注解 `@SofaService` 发布了接口为 SampleJvmService 的 JVM 服务，关键代码：
```java
package me.qlong.tech.service.impl;

import com.alipay.sofa.runtime.api.annotation.SofaService;
import me.qlong.tech.service.SampleJvmService;
import org.springframework.stereotype.Component;

@SofaService
@Component
public class AppTwoSampleService implements SampleJvmService{
    public String service() {
        return "App Two";
    }
}
```

## 依赖
Biz 之间使用内部 JVM 服务进行通信需要依赖 SOFARuntime 包及其对应的 Ark Plugin，需要添加如下依赖：
```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
    <classifier>ark-plugin</classifier>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>
```
关于 JVM 服务的发布和引用推荐阅读[SOFABoot 文档](../Module-Service), 在 Jarslink2.0 中推荐使用注解的方式。

## 演示
+ cd biz-jvm-invocation-sample/facade && mvn clean install 
在 facade 应用根目录中执行 mvn clean install 命令，把 facade 包安装到本地 maven 仓库，以便在 app-one 和 app-two 中添加 facade 依赖：
```xml
<!--service facade-->
<dependency>
    <groupId>me.qlong.tech</groupId>
    <artifactId>facade</artifactId>
    <version>1.0.0</version>
</dependency>
```

+ cd biz-jvm-invocation-sample/app-one && mvn clean package
在 app-one 应用根目录中执行 mvn clean package 命令，将应用打包成 Ark 包和 Biz 包，文件将输出到 biz-jvm-invocation-sample/app-one/target 目录

+ cd biz-jvm-invocation-sample/app-two && mvn clean package
在 app-two 应用根目录中执行 mvn clean package 命令，将应用打包成 Ark 包和 Biz 包，文件将输出到 biz-jvm-invocation-sample/app-two/target 目录

+ 使用 java -jar 启动 app-one 应用的 Ark 包
+ 启动完成之后，浏览器访问 http://localhost:8080/hello ,提示找不到接口为 me.qlong.tech.service.SampleJvmService 的 JVM 服务，页面显示错误信息如下：
```text
There was an unexpected error (type=Internal Server Error, status=500).
JVM Reference[interface me.qlong.tech.service.SampleJvmService#] cant not find the corresponding JVM service. Please check if there is a SOFA deployment publish the corresponding JVM service. If this exception occurred when the application starts up, please add Require-Module to SOFA deployment's MANIFEST.MF to indicate the startup dependency of SOFA modules.
```
+ 上述报错原因是因为处理 /hello 请求时，会触发 app-one 去调用接口为 me.qlong.tech.service.SampleJvmService 的 JVM 服务，因为此时没有任何其他 Biz 发布这个服务，因此会提示找不到该 JVM 服务的报错。
+ 使用 telnet localhost 1234 进入 Jarslink2.0 指令交互界面，并执行 install -b 指令，安装启动 app-two 的 Biz 包。
+ 重新再浏览器访问 http://localhost:8080/hello , 页面显示 "App Two"。表明此次请求触发 app-one 顺利调用到了 app-two 发布的接口为 me.qlong.tech.service.SampleJvmService 的 JVM 服务。

