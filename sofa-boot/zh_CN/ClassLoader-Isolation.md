SOFABoot 提供了类隔离框架 SOFAArk, 弥补了 Spring Boot 在类隔离能力上的缺失，用以解决在实际开发中常见的类冲突、包冲突问题，详细请参考 [SOFAArk](https://github.com/sofastack/sofa-ark)

在 SOFABoot 工程中使用类隔离能力，只需两步操作；配置 `sofa-ark-maven-plugin` 打包插件以及引入 `sofa-ark-springboot-starter` 类隔离框架依赖；

## 配置 Maven 打包插件

官方提供了 `Maven` 插件 - `sofa-ark-maven-plugin` ，只需要简单的配置项，即可将 SpringBoot 工程打包成标准格式规范的可执行 Ark 包，插件坐标为：

```xml
<plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```

配置模板如下：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-ark-maven-plugin</artifactId>
            <executions>
                <execution>
                    <id>default-cli</id>
                    
                    <!--goal executed to generate executable-ark-jar -->
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                    
                    <configuration>
                        <!--specify destination where executable-ark-jar will be saved, default saved to ${project.build.directory}-->
                        <outputDirectory>./target</outputDirectory>
                        
                        <!--default none-->
                        <arkClassifier>executable-ark</arkClassifier>
                        
                        <!-- all class exported by ark plugin would be resolved by ark biz in default, if 
                        configure denyImportClasses, then it would prefer to load them by ark biz itself -->
                        <denyImportClasses>
                            <class>com.alipay.sofa.SampleClass1</class>
                            <class>com.alipay.sofa.SampleClass2</class>
                        </denyImportClasses>
                        
                        <!-- Corresponding to denyImportClasses, denyImportPackages is package-level -->
                        <denyImportPackages>
                            <package>com.alipay.sofa</package>
                            <package>org.springframework</package>
                        </denyImportPackages>
                        
                        <!-- denyImportResources can prevent resource exported by ark plugin with accurate 
                        name to be resolved -->
                        <denyImportResources>
                            <resource>META-INF/spring/test1.xml</resource>
                            <resource>META-INF/spring/test2.xml</resource>
                        </denyImportResources>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
插件配置项解释：

+ `outputDirectory`: 执行 `mvn package` 命令后，指定打出来的 ark 包存放目录，默认存放至 ${project.build.directory}
+ `arkClassifier`: 执行 `mvn depleoy` 命令后，指定发布到仓库的 ark 包的maven坐标的 `classifer` 值, 默认为空；我们推荐配置此配置项用于和普通的 Fat Jar 加以名字上区别；
+ `denyImportClasses`: 默认情况下，应用会优先加载 ark plugin 导出的类，使用该配置项，可以禁止应用从 ark plugin 加载其导出类；
+ `denyImportPackages`: 对应上述的 `denyImportClasses`, 提供包级别的禁止导入； 
+ `denyImportResources`: 默认情况下，应用会优先加载 ark plugin 导出的资源，使用该配置项，可以禁止应用从 ark plugin 加载其导出资源；


## 添加类隔离框架依赖

在实际开发中，为了在跑测试用例时使用 SOFABoot 类隔离能力，需要在 SOFABoot 工程中添加如下的依赖：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

根据 SpringBoot 依赖即服务的原则，添加该依赖之后，应用启动之前，会优先启动 SOFABoot 类隔离容器；

SOFABoot 的类隔离框架会自动检测应用中是否有引入 Ark Plugin（即需要被隔离的jar包，详情请参考 [SOFAArk](https://github.com/sofastack/sofa-ark)）, 并隔离加载；例如为了避免 SOFABoot 官方提供的 SOFARPC 组件和应用产生依赖冲突，SOFABoot提供了 SOFARPC 组件对应的 ark plugin 版，用户如果需要隔离 SOFARPC，只需要添加如下组件：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-plugin</artifactId>
</dependency>
```

如此，在运行时，SOFABoot 的类隔离容器会自动隔离 SOFARPC 和其他应用依赖，避免可能存在的包冲突；当然开发者也可以独立开发自身所需 ark plugin 包，详情参考[SOFAArk](https://github.com/sofastack/sofa-ark)


## 运行

### 在 IDE 中运行

直接通过 `main` 函数启动即可，可以看到类似如下的启动成功日志：

```java
2018-04-07 10:51:59,646 INFO  main                             - Begin to start ArkServiceContainer
2018-04-07 10:52:00,550 INFO  main                             - Init Service: com.alipay.sofa.ark.container.service.plugin.PluginDeployServiceImpl
2018-04-07 10:52:00,551 INFO  main                             - Init Service: com.alipay.sofa.ark.container.service.classloader.ClassloaderServiceImpl
2018-04-07 10:52:00,557 INFO  main                             - Finish to start ArkServiceContainer
2018-04-07 10:52:00,571 INFO  main                             - Start to process pipeline stage: com.alipay.sofa.ark.container.pipeline.HandleArchiveStage
2018-04-07 10:52:00,624 INFO  main                             - Finish to process pipeline stage: com.alipay.sofa.ark.container.pipeline.HandleArchiveStage
2018-04-07 10:52:00,624 INFO  main                             - Start to process pipeline stage: com.alipay.sofa.ark.container.pipeline.DeployPluginStage
2018-04-07 10:52:00,626 INFO  main                             - Start to deploy plugin: sample-ark-plugin
starting in sample ark plugin activator
2018-04-07 10:52:00,645 INFO  main                             - Service: com.alipay.sofa.ark.sample.facade.SamplePluginService publish by: PluginServiceProvider{pluginName='sample-ark-plugin', priority=1000} succeed
2018-04-07 10:52:00,647 INFO  main                             - Finish to deploy plugin: sample-ark-plugin
2018-04-07 10:52:00,647 INFO  main                             - Finish to process pipeline stage: com.alipay.sofa.ark.container.pipeline.DeployPluginStage
2018-04-07 10:52:00,647 INFO  main                             - Start to process pipeline stage: com.alipay.sofa.ark.container.pipeline.DeployBizStage
2018-04-07 10:52:00,650 INFO  main                             - Begin to start biz: Startup In IDE
```

### 打包运行

使用 `mvn clean install` 将工程打包具体类隔离能力的 Fat Jar。打包之后的文件格式类似如下：

```
.
├── classes
│   └── com
│       └── alipay
│           └── sofa
│               └── boot
│                   └── examples
│                       └── demo
│                           └── isolation
│                               └── SofaBootClassIsolationDemoApplication.class
├── generated-sources
│   └── annotations
├── maven-archiver
│   └── pom.properties
├── maven-status
│   └── maven-compiler-plugin
│       ├── compile
│       │   └── default-compile
│       │       ├── createdFiles.lst
│       │       └── inputFiles.lst
│       └── testCompile
│           └── default-testCompile
│               └── inputFiles.lst
├── sofaboot-sample-with-isolation-2.4.0-ark-biz.jar
├── sofaboot-sample-with-isolation-2.4.0-executable-ark.jar
└── sofaboot-sample-with-isolation-2.4.0.jar
```

其中 `sofaboot-sample-with-isolation-2.4.0-executable-ark` 就是一个具备类隔离能力的 Fat Jar，我们可以直接通过 `java -jar` 的方式将其启动。在服务器或者控制台上执行 `java -jar sofaboot-sample-with-isolation-2.4.0-executable-ark` 可以看到类似如下的启动成功日志：

```
2018-04-07 10:57:48.033  INFO 8488 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-04-07 10:57:48.042  INFO 8488 --- [           main] .i.SofaBootClassIsolationDemoApplication : Started SofaBootClassIsolationDemoApplication in 7.432 seconds (JVM running for 8.99)
Ark container started in 8797 ms.
```

## 测试
为了在跑测试用例时使用 SOFABoot 类隔离能力，需要额外引入 SOFABoot 官方提供的 `test-sofa-boot-starter` 依赖，使用 `SofaBootRunner` 和 `SofaJUnit4Runner` 编写集成测试和单元测试，这也是 SOFABoot 推荐测试用例的编写方式.

使用 `SofaBootRunner` 和 `SofaJUnit4Runner` 的好处是会自动感知应用是否使用了类隔离能力，开发者只需添加或者删除 SOFABoot 类隔离依赖包：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

即可完成和 SOFABoot 类隔离能力的集成和剥离，使用详情如下：

#### SofaBootRunner
集成测试示例代码：

```java
@RunWith(SofaBootRunner.class)
@SpringBootTest(classes = SofaBootClassIsolationDemoApplication.class)
public class IntegrationTestCase {

    @Autowired
    private SampleService sampleService;

    @Test
    public void test() {
        Assert.assertTrue("service".equals(sampleService.service()));
    }

}
```

`SofaBootRunner` 会检测应用是否引入

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

依赖；根据 Spring Boot 依赖即服务的原则，如果检测到 `sofa-ark-springboot-starter` 依赖，`SofaBootRunner` 会使用 SOFABoot 类隔离能力，否则和原生的 `SpringRunner` 无异；

#### SofaJUnit4Runner
单元测试示例代码：

```java
@RunWith(SofaJUnit4Runner.class)
public class UnitTestCase {

    @Test
    public void test() {
        Assert.assertTrue(true);
    }
}
```

`SofaJUnit4Runner` 同样会检测应用是否引入 `sofa-ark-springboot-starter` 依赖；根据 Spring Boot 依赖即服务的原则，如果检测到 `sofa-ark-springboot-starter` 依赖，`SofaJUnit4Runner` 会使用 SOFABoot 类隔离能力，否则和原生的 `JUnit4` 无异；

#### 自定义 Runner
在编写测试用例时，有时需要指定特殊的 Runner，为了统一编码风格，可以借助注解 `@DelegateToRunner` 配合 `SofaBootRunner` 和 `SofaJUnit4Runner` 使用，示例代码：

```java
@RunWith(SofaJUnit4Runner.class)
@DelegateToRunner(BlockJUnit4ClassRunner.class)
public class UnitTestCaseWithoutArk {

    @Test
    public void test() {
        Assert.assertFalse(true);
    }

}
```

相当于如下使用

```java
@RunWith(BlockJUnit4ClassRunner.class)
public class UnitTestCaseWithoutArk {

    @Test
    public void test() {
        Assert.assertFalse(true);
    }

}
```

