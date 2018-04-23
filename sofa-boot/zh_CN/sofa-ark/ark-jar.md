---
title: Ark 包
---

本小节将介绍标准 `Ark 包` 的目录结构，以及如何使用官方插件 `sofa-ark-maven-plugin` 打包并发布 `Ark 包`。

## Maven 插件
官方提供 `Maven` 插件 `sofa-ark-maven-plugin` 可以将普通 Java 工程或者 Spring Boot 工程打包成标准格式 `Ark 包` ；基于 [Fat Jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html#executable-jar-jar-file-structure) 技术，使用 `java -jar` 命令可以直接启动 `Ark 包` 。 `Maven` 插件坐标为：

```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
    <version>0.2.0</version>
</plugin>
```

## Goals
`sofa-ark-maven-plugin` 插件提供 `goal: repackage`， 可以将工程打包成可执行的 `Ark 包`，如下配置：

```xml
<build>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-maven-plugin</artifactId>
        <version>0.2.0</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>repackage</goal>
                </goals>
            </excution>
        </executions>
        <configuration>
            <!-- 配置信息 -->
        </configuration>
    </plugin>
</build>
```
## 完整配置模板
完整的 `sofa-ark-maven-plguin` 插件配置模板如下：

```xml
<plugins>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-maven-plugin</artifactId>
        <version>0.1.0</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>repackage</goal>
                </goals>
                <configuration>

                    <!--ark 包和 ark biz 的打包存放目录，默认为工程 build 目录-->
                    <outputDirectory>../target</outputDirectory>

                    <!--生成 ark 包文件名称，默认为 ${artifactId}-->
                    <finalName>demo-ark</finalName>

                    <!--是否跳过执行 goal:repackage，默认为false-->
                    <skip>false</skip>

                    <!--是否打包、安装和发布 ark biz，详细参考 Ark Biz 文档，默认为false-->
                    <attach>true</attach>

                    <!--设置 ark 包的 classifier，默认为空-->
                    <arkClassifier>ark-classifier</arkClassifier>

                    <!--设置 ark biz 的 classifier，默认为 ark-biz-->
                    <bizClassifier>ark-biz-classifier</bizClassifier>

                    <!--打包 ark biz 时，排除指定的包依赖；格式为: ${groupId:artifactId} 或者 ${groupId:artifactId:classifier}-->
                    <excludes>
                        <exclude>org.apache.commons:commons-lang3</exclude>
                    </excludes>

                    <!--打包 ark biz 时，排除和指定 groupId 相同的包依赖-->
                    <excludeGroupIds>
                        <excludeGroupId>org.springframework</excludeGroupId>
                    </excludeGroupIds>

                    <!--打包 ark biz 时，排除和指定 artifactId 相同的包依赖-->
                    <excludeArtifactIds>
                        <excludeArtifactId>sofa-ark-spi</excludeArtifactId>
                    </excludeArtifactIds>
                    
                    <!--打包 ark biz 时，配置不从 ark plugin 索引的类；默认情况下，ark biz 会优先索引所有 ark plugin 的导出类，
                    添加该配置后，ark biz 将只在ark biz内部加载该类，不再优先委托 ark plugin 加载--> 
                    <denyImportClasses>
                        <class>com.alipay.sofa.SampleClass1</class>
                        <class>com.alipay.sofa.SampleClass2</class>
                    </denyImportClasses>
                    
                    <!--对应 denyImportClasses 配置，可以配置包级别-->
                    <denyImportPackages>
                        <package>com.alipay.sofa</package>
                        <package>org.springframework</package>
                    </denyImportPackages>
                    
                    <!--打包 ark biz 时，配置不从 ark plugin 索引的资源；默认情况下，ark biz 会优先索引所有 ark plugin 的导出资源,
                    添加该配置后，ark biz 将只在ark biz内部寻找该资源，不在从 ark plugin 查找-->
                    <denyImportResources>
                        <resource>META-INF/spring/test1.xml</resource>
                        <resource>META-INF/spring/test2.xml</resource>
                    </denyImportResources>

                </configuration>
            </execution>
        </executions>
    </plugin>
</plugins>
```
配置项含义如下：

+ `outputDirectory`: ark 包和 ark biz 的打包存放目录，默认为工程 build 目录;

+ `finalName`: 生成 ark 包文件名称，默认为 ${artifactId};

+ `skip`: 是否跳过执行 goal:repackage，默认为false;

+ `attach`: 是否打包、安装和发布 ark biz，详细参考 Ark Biz 文档，默认为false;

+ `arkClassifier`: 设置 ark 包的 classifier，默认为空;

+ `bizClassifier`: 设置 ark biz 的 classifier，默认为 ark-biz;

+ `excludes`: 打包 ark biz 时，排除指定的包依赖；格式为: ${groupId:artifactId} 或者 ${groupId:artifactId:classifier};

+ `excludeGroupIds`: 打包 ark biz 时，排除和指定 groupId 相同的包依赖;
+ `excludeArtifactIds`: 打包 ark biz 时，排除和指定 artifactId 相同的包依赖;

+ `denyImportClasses`: 打包 ark biz 时，配置不从 ark plugin 索引的类；默认情况下，ark biz 会优先索引所有 ark plugin 的导出类，添加该配置后，ark biz 将只在ark biz内部加载该类，不再优先委托 ark plugin 加载;

+ `denyImportPackages`: 对应 denyImportClasses 配置，可以配置包级别;

+ `denyImportResources`: 打包 ark biz 时，配置不从 ark plugin 索引的资源；默认情况下，ark biz 会优先索引所有 ark plugin 的导出资源, 添加该配置后，ark biz 将只在ark biz内部寻找该资源，不在从 ark plugin 查找

## 构建
以 Spring Boot Web 工程为例，为了生成标准的 `Ark 包` ，只需要两步操作
* 参考配置模板，在 Web 模块的 pom.xml 文件中配置 `sofa-ark-maven-plugin` 插件；
* 在 Web 模块目录或应用根目录中敲击 `mvn pacakge` 命令，即可在配置的 `outputDirectory` 目录中生成 `Ark 包` 和 `Ark Biz` 文件；默认命名分别为 Web 模块 `${artifactId}` 和 `${artifactId}-ark-biz` ; 也可以分别通过 `arkClassifier` 和 `bizClassifier` 设置 classifier；敲击 `mvn install` 命令，则会安装 `Ark 包` 到本地仓库，如果设置了 `attach` 为 `true`, 会同时把 `ark biz` 安装到本地仓库；
  
## 发布
在工程主 pom 中配置仓库地址，然后敲击 `mvn deploy`命令，即可发布该 `Ark 包`；需要强调的是，如果设置了 `attach` 为 `true`，`Ark Biz` 也会发布到仓库，此外 `Ark Biz` 发布包默认会带上 `classifier = ark-biz`，可以通过 `bizClassifier` 设置 `ark biz` 的 classifier ；

## Ark 包典型目录结构
下面是一个普通的 `Spring Boot Web` 应用打包生成的 `Ark 包` 目录结构

```text
.
├── META-INF
│   └── MANIFEST.MF
├── SOFA-ARK
│   ├── biz
│   │   └── sofa-boot-demo-web-1.0-SNAPSHOT-sofa-ark-biz.jar
│   ├── container
│   │   ├── META-INF
│   │   │   └── MANIFEST.MF
│   │   ├── com
│   │   │   └── alipay
│   │   │       └── sofa
│   │   │           └── ark
│   │   └── lib
│   │       ├── aopalliance-1.0.jar
│   │       ├── guava-16.0.1.jar
│   │       ├── guice-4.0.jar
│   │       ├── guice-multibindings-4.0.jar
│   │       ├── javax.inject-1.jar
│   │       ├── log4j-1.2.17.jar
│   │       ├── slf4j-api-1.7.21.jar
│   │       ├── slf4j-log4j12-1.7.21.jar
│   │       ├── sofa-ark-archive-0.1.0.jar
│   │       ├── sofa-ark-common-0.1.0.jar
│   │       ├── sofa-ark-container-0.1.0.jar
│   │       ├── sofa-ark-exception-0.1.0.jar
│   │       ├── sofa-ark-spi-0.1.0.jar
│   │       └── sofa-common-tools-1.0.11.jar
│   └── plugin
│       └── sofa-ark-rpc-plugin-2.2.5-ark-plugin.jar
└── com
    └── alipay
        └── sofa
            └── ark
                ├── bootstrap
                │   ├── ArkLauncher.class
                │   ├── ClasspathLauncher$ClassPathArchive.class
                │   ├── ClasspathLauncher.class
                │   ├── ContainerClassLoader.class
                │   ├── EntryMethod.class
                │   ├── ExecutableArchiveLauncher.class
                │   ├── Launcher.class
                │   ├── MainMethodRunner.class
                │   └── SofaArkBootstrap.class
                ├── loader
                │   ├── DirectoryBizModuleArchive.class
                │   ├── ExecutableArkBizJar$1.class
                │   ├── ExecutableArkBizJar$2.class
                │   ├── ExecutableArkBizJar$3.class
                │   ├── ExecutableArkBizJar.class
                │   ├── JarBizModuleArchive$1.class
                │   ├── JarBizModuleArchive.class
                │   ├── JarContainerArchive$1.class
                │   ├── JarContainerArchive.class
                │   ├── JarPluginArchive$1.class
                │   ├── JarPluginArchive.class
                │   ├── archive
                │   ├── data
                │   └── jar
                └── spi
                    └── archive
```

上述目录结构相关文件和目录说明如下：

* `META-INF/MANIFEST.MF` : 记录 `Ark` 包元信息，其中最关键的信息是 `Ark` 包的启动入口类 `Main-Class` ；文件内容类似如下：

  ```manifest
  Manifest-Version: 1.0
  Implementation-Title: sofa-ark-all
  Implementation-Version: 0.1.0
  Archiver-Version: Plexus Archiver
  Built-By: qilong.zql
  Sofa-Ark-Version: 0.1.0
  Specification-Title: sofa-ark-all
  Implementation-Vendor-Id: com.alipay.sofa
  Main-Class: com.alipay.sofa.ark.bootstrap.ArkLauncher
  Ark-Container-Root: SOFA-ARK/container/
  Created-By: Apache Maven 3.2.5
  Build-Jdk: 1.8.0_101
  Specification-Version: 0.1.0
  ArkVersion: 0.1.0
  Timestamp: 2018-03-13T18:13:06Z
  ```
可以看到，`Ark 包` 启动入口类是 `com.alipay.sofa.ark.bootstrap.ArkLauncher`

* `com/alipay/sofa/ark/*` : 存放使用 `java -jar` 命令启动 `Ark 包`  所必需的引导类；

* `SOFA-ARK/container` : 存放 `ark container` 容器依赖包，实际上是如下依赖包的解压目录；

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-all</artifactId>
    <version>0.2.0</version>
</dependency>
```

* `SOFA-ARK/biz` : 存放所有的 `Ark Biz` 包，因为 demo 工程中没有依赖其他的 `Ark Biz` , 因此只有自身的 `Ark Biz`

* `SOFA-ARK/plugin` : 存放应用依赖的所有 `Ark Plugin` 包，demo 工程只依赖一个 rpc `Ark Plugin`


