## 简介
本小节将介绍 `Ark Biz` 目录结构，以及如何使用官方插件 `sofa-ark-maven-plugin` 打包并发布 `Ark Biz`。

`Ark Biz` 包和 `Ark 包` 都是使用 Maven 插件 `sofa-ark-maven-plugin` 打包生成；工程应用在配置该插件时，默认情况下只会打包发布 `Ark 包`， 只有在配置参数 `attach` 为 true 时，才会打包发布 `Ark Biz`：

```xml
<build>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-maven-plugin</artifactId>
        <version>${sofa.ark.version}</version>
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

那 `Ark Biz` 和 `Ark 包` 有什么区别呢？ 简单来说，`Ark Biz` 是工程应用所有资源的组织单元，它包含了应用启动所需的所有资源，详细可参考下文描述的 `Ark Biz` 目录格式；而工程应用打出来的 `Ark 包`，是一个通过 `java -jar` 启动，运行在 SOFAArk 容器的 Fat Jar，不仅包含应用工程对应的 `Ark Biz`，也包含 `Ark Container`，以及应用依赖的 `Ark Plugin`；

通常情况，只需要发布 `Ark 包` 即可，但是 SOFAArk 是支持运行多个 `Ark Biz`的，因此如果开发者希望自己应用的 `Ark Biz` 包能够被其他应用直接当成 Jar 包依赖，进而运行在同一个 SOFAArk 容器之上，那么就需要打包发布 `Ark Biz 包`；


## Ark-Biz 典型目录结构

```text
.
├── META-INF
│   ├── MANIFEST.MF
│   ├── maven
│   │   └── me.qlong.tech
│   │       └── sofa-boot-demo3-web
│   │           ├── pom.properties
│   │           └── pom.xml
│   └── sofa-boot-demo3
│       └── sofa-boot-demo3-web.xml
├── com
│   └── alipay
│       └── sofa
│           └── ark
│               └── biz
│                   └── mark
├── config
│   ├── application-dev.properties
│   ├── application-test.properties
│   └── application.properties
├── lib
│   ├── spring-beans-4.3.4.RELEASE.jar
│   ├── spring-boot-1.4.2.RELEASE.jar
│   ├── spring-boot-autoconfigure-1.4.2.RELEASE.jar
│   ├── spring-boot-devtools-1.4.2.RELEASE.jar
│   ├── spring-boot-starter-1.4.2.RELEASE.jar
│   ├── spring-boot-starter-logging-1.4.2.RELEASE.jar
│   ├── spring-boot-starter-tomcat-1.4.2.RELEASE.jar
│   ├── spring-boot-starter-web-1.4.2.RELEASE.jar
│   ├── spring-context-4.3.4.RELEASE.jar
│   ├── spring-core-4.3.4.RELEASE.jar
│   ├── spring-expression-4.3.4.RELEASE.jar
│   ├── spring-web-4.3.4.RELEASE.jar
│   ├── ...
│   ├── ...
│   ├── ...
│   └── velocity-1.7.jar
├── logback-spring.xml
├── me
│   └── qlong
│       └── tech
│           └── SOFABootWebSpringApplication.class
└── static
    └── index.html
```

上述目录结构相关文件和目录说明如下：

普通的 Java 工程或者 Spring Boot Core/Web 工程都可以打包成 `Ark Biz`；`Ark Biz` 没有固定的目录格式，它只是在原来 Jar 包结构基础上新增两个目录文件：

* `com/alipay/sofa/ark/biz/mark` : 标记文件，标记该 Jar 包是 `sofa-ark-maven-plugin` 打包生成的 `Ark Biz` 文件；

* `lib/` : lib 目录存放工程应用的三方依赖，