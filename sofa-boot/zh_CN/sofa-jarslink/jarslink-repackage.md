# 简介
理解 Jarslink2.0 的使用方式，你需要对 [SOFAArk 框架](https://github.com/sofastack/sofa-ark)以及 [Ark 包](../sofa-ark/ark-jar)和 [Ark Biz](../sofa-ark/ark-biz) 的打包方式有一定的了解。

为了保证阅读的连贯性，在这里会粗略的描述应用使用 Jarslink2.0 的打包逻辑，官方推荐先跳转到上述链接进行必要的背景知识学习。

Jarslink2.0 要求应用是 [Spring Boot](https://spring.io/projects/spring-boot) 或者 [SOFABoot](https://github.com/sofastack/sofa-boot) 应用类型，在介绍新的应用打包方式之前，先来看下为什么 Spring Boot/ SOFABoot 应用在使用 Jarslink2.0 之后，需要引入新的打包方式。

## 背景
Jarslink2.0 运行时是作为 SOFAArk 框架的 [Ark Plugin](../sofa-ark/ark-plugin) 工作, 因此使用 Jarslink2.0 必须要引入 SOFAArk 框架，只有在 SOFAArk 容器启动之后，才会加载并启动 Jarslink2.0 插件。我们知道，官方 Spring Boot 工程使用插件：
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```
会打出一个可执行的 FatJar，该 FatJar 包含应用运行时所需的所有依赖、配置等资源，该 FatJar 入口方法即是 Spring Boot 应用的 main 方法。SOFABoot 工程和 Spring Boot 工程打包逻辑一样。

应用接入 Jarslink2.0 之后，由于 Jarslink2.0 需要依赖 SOFAArk 框架，因此使用新的打包方式打出来的 FatJar 需要包含 SOFAArk 容器，而且 FatJar 的入口方法也需要换成 SOFAArk 容器的启动方法，理由是该 FatJar 的执行需要先启动 SOFAArk 容器，然后 SOFAArk 容器依次启动所有的 Ark Plugin，最后启动应用。因此需要一种新的打包方式，SOFAArk 提供了打包插件：
```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```
负责将 Spring Boot/SOFABoot 应用打包成一个可执行的 FatJar，称之为 [Ark 包](../sofa-ark/ark-jar)。

## 打包类型
在上一小结，我们描述了为什么使用 Jarslink2.0 需要引入区别于 Spring Boot 官方的打包方式，并引出了应用的第一种打包类型，Ark 包。下面我们总结下 Ark 包具有的特性：

+ Ark 包是 SOFAArk 框架自定义的一种可执行的 FatJar 包格式，具体可[参考文档](../sofa-ark/ark-jar)。
+ Ark 包除了包含应用运行时所需的所有配置、依赖等资源，类似 Spring Boot的打包逻辑；而且包含了应用依赖的 Ark Plugin 以及 SOFAArk 框架。其中 SOFAArk 框架不需要应用开发者使用传统的 `<dependency/>` 标签形式在应用中引入，而是上述打包插件 `sofa-ark-maven-plugin` 自动打入，SOFAArk 框架版本即为打包插件版本。

为了理解简单，可以认为 Ark 包 = Spring Boot FatJar + SOFAArk框架 + Ark Plugin。在新的打包逻辑中，为了更合理组织这部分 Spring Boot Fatjar，SOFAArk 重新组织成了另一种组织格式，这就引出了应用的第二种打包类型，Biz 包。关于 Biz 包的特性总结如下：

+ Biz 包是 SOFAArk 框架定义的一种 FatJar 包格式，具体可[参考文档](../sofa-ark/ark-biz)。

+ Biz 包类似 Spring Boot 打包插件打出的 FatJar，包含的应用运行时所需的所有配置、依赖等资源。唯一不同的是，请注意，它不能通过 `java -jar` 命令直接运行。

因此，实际上可以认为 Ark 包 = Biz 包 + SOFAArk 框架 + Ark Plugin

综述，应用通过 SOFAArk 提供的打包插件：
```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```
可以打成两种类型格式包，分别是 Ark 包和 Biz 包。

## 打包配置
关于打包插件 `sofa-ark-maven-plugin` 的详细使用可以[参考文档](../sofa-ark/ark-jar)。这里贴一个简单的打包配置，配置如下：

```xml
<build>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-maven-plugin</artifactId>
        <version>${sofa.ark.plugin.version}</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>repackage</goal>
                </goals>
            </excution>
        </executions>
        <configuration>
            <attach>false</attach>
        </configuration>
    </plugin>
</build>
```

请注意 attach 这个配置，它的作用主要影响 Biz 包的安装和发布。当 attach 配置发布为 true 时，执行 mvn install 命令时，Biz 包会安装到本地 maven 仓库；执行 mvn deploy 命令时，Biz 包也会被上传到远程 maven 仓库。Biz 包的 GAV 坐标由 pom.xml 文件决定，并额外会带上 classifier=ark-biz 的属性。关于 classifier 的作用，请参考[StackOverFlow 回答](https://stackoverflow.com/questions/20909634/what-is-the-purpose-of-mavens-dependency-declarations-classifier-property)

