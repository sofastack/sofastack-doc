> [工程地址](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/spring-boot-transform-sample)

## 简介
Spring Boot 和 SOFABoot 都可以使用 Jarslink2.0，只需要添加指定依赖即可。为了使用便利，推荐以 SOFABoot 工程形式使用 Jarslink2.0。本样例工程意在演示如何快速的将 Spring Boot 工程改造成 SOFABoot 工程。

## 改造
在 [Spring Boot 官网](https://start.spring.io/)创建好一个 Spring Boot 的工程之后，接下来只需要引入 SOFABoot 的依赖，首先修改 maven 项目的配置文件 pom.xml，将
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/> 
</parent>
```
替换为
```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>2.5.0-SNAPSHOT</version>
</parent>
```

**注意: 目前 Jarslink 2.0.0 还处于快照版本，它依赖的 SOFABoot 2.5.0 版本也将近期发布，目前需要引入 SOFABoot 2.5.0-SNAPSHOT 版本依赖。SNAPSHOT 包拉取需要特殊的配置，详细请参考[FAQ: 如何配置拉取 SNAPSHOT 依赖包](./faq)**

然后，添加一个 Spring Boot 官方或者 SOFABoot 官方的 Starter,如：
```xml
<dependencies>
    <!-- Jarslink2.0 -->
    <dependency>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-jarslink-ark-starter</artifactId>
        <classifier>ark-plugin</classifier>
        <version>2.0.0-SNAPSHOT</version>
    </dependency>

    <!-- Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```
为了将应用打包成 Ark 包或者 Biz 包，需要在主 pom.xml 文件中，配置 sofa-ark-maven-plugin 打包插件，注意请删除 Spring Boot 原生的打包插件配置:
```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-ark-maven-plugin</artifactId>
            <executions>
                <execution>
                    <!--goal executed to generate executable-ark-jar -->
                    <goals>
                        <goal>repackage</goal>
                    </goals>

                    <configuration>
                        <!--specify destination where executable-ark-jar will be saved, default saved to ${project.build.directory}-->
                        <outputDirectory>./target</outputDirectory>

                        <!--default none-->
                        <arkClassifier>executable-ark</arkClassifier>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
最后，在工程的 application.properties 文件下添加一个 SOFABoot 必须要使用的参数，包括spring.application.name 用于标示当前应用的名称；logging path 用于指定日志的输出目录。
```text
# Application Name
spring.application.name=spring-boot-transform-sample

# logging path
logging.path=./logs
```

如此，即将一个普通的 Spring Boot 工程改造成了一个 SOFABoot 工程，并集成了 Jarslink2.0 组件。

### 运行
由于在打包插件配置了输出目录为 ./target，因此在应用根目录执行 mvn clean install 命令之后，会发现在 ./target 目录中生成了应用打包后的 Ark 包和 Biz包，形如：
+ spring-boot-transform-sample-${version}-executable-ark.jar 这是 Ark 包
+ spring-boot-transform-sample-${version}-ark-biz.jar 这是 Biz 包

使用 java -jar 即可启动该 Ark 包。更多关于打包信息请查看[应用打包](./jarslink-repackage)。