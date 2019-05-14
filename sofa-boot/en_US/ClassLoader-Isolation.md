SOFABoot provides a class isolation framework SOFAArk, giving Spring Boot a class isolation ability to resolve class or package conflicts in the development. For detailed information, please refer to:[SOFAArk](https://github.com/sofastack/sofa-ark)

To use this feature in SOFABoot projects, we need only two steps: configure the `sofa-ark-maven-plugin` plugins for packaging and add `sofa-ark-springboot-starter` dependencies of the class isolation framework.

## Configure Maven packaging plugins

The `Maven` plugins - `sofa-ark-maven-plugin` are available on the Central Repository. Through simple configurations, a SpringBoot project can be wrapped into an executable Ark package in the standard format. The coordinate of `sofa-ark-maven-plugin` is:

```xml
<plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```

The configuration template is described as follows:

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
Description of plugin configuration:

+ `outputDirectory`: Execute `mvn package` and then specify a directory to store the Ark package. The default directory is ${project. Build. Directory}.
+ `arkClassifier`: Execute `mvn docleoy`, and then specify the coordinates of Maven repositories to locate the Ark package by setting the `classfaulter` value (the default is empty). We recommend that you configure this to give a different name from the ordinary Fat jar;
+ `denyImportClasses`: By default, the application will first load the classes that the Art plugin exports. It can be configured to deny such export.
+ `denyImportPackages`: Corresponding to the above `denyImportClasses`, it can be configured to deny import at the package level; 
+ `denyImportResources`: By default, the application will first load the resources that the Art plugin exports. It can be configured to deny such export.


## Add dependencies for the class isolation framework

To use SOFABoot class isolation capabilities when running test cases in the development process, we need to add the following dependencies into the SOFABoot project:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

In accordance with SpringBoot's dependency-as-a-service principle, after adding those dependencies, the SOFABoot class isolation container will be started before the application runs.

SOFABoot's class isolation framework will automatically check whether Ark plugins (or jars that need to be isolated; for detailed information, please refer to [SOFAArk](https://github.com/sofastack/sofa-Ark)) has been loaded into the application, and isolated from loading; for example, to avoid dependency conflicts between the official SOFARPC components provided by SOFABoot and the application, SOFABoot provides an Ark plugin version of SOFARPC. If you want to isolate SOFARPC, you only need to add the following components:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-starter</artifactId>
    <classifier>ark-plugin</classifier>
</dependency>
```

By doing so, SOFABoot's class isolation container will automatically isolate SOFARPC and other application dependencies at run time to avoid potential package conflicts. Developers, of course, can also develop their own Ark plugin packages (refer to [SOFAArk](https://github.com/sofastack/sofa-Ark) for details).


## Running

### Running in IDE

Simply run the `main` function. If successful, the following startup log will appear:

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

### Packaging to run

Select `mvn clean install` to package the project into a fat jar with class isolation features. The packaged file format is like this:

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

Here, `sofabot-sample-with-isolation-2.4.0-executable-ark` is a Fat Jar with the class isolation capability, so we can simply start it up with the `java -jar` command. Execute `java -jar sofaboot-sample-with-isolation-2.4.0-executable-ark` on the server or console, and a successful startup log will appear like this:

```
2018-04-07 10:57:48.033  INFO 8488 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
2018-04-07 10:57:48.042  INFO 8488 --- [           main] .i.SofaBootClassIsolationDemoApplication : Started SofaBootClassIsolationDemoApplication in 7.432 seconds (JVM running for 8.99)
Ark container started in 8797 ms.
```

## Testing
To run the test cases with SOFABoot's class isolation ability, you need to add an extra dependency—SOFABoot's official `test-sofa-boot-starter`—and write integration testing and unit testing code using `SofaBootRunner` and `SofaJUnit4Runner`, which is also a coding style for test cases recommended by SOFABoot.

With the two modules, the system can automatically check whether the application has used the class isolation capability. Developers can achieve this by simply adding or deleting the SOFABoot class isolation dependencies

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

to complete the integration and separation of the SOFABoot class isolation. See the following for details:

#### SofaBootRunner
Sample code for integration testing:

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

The `SofaBootRunner` will check whether the application has introduced dependencies;

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-springboot-starter</artifactId>
</dependency>
```

In accordance with Spring Boot's dependency-as-a-service principle, if the `sofa-Ark-springboot-starter` dependency is detected, then the `SofaBootRunner` will enable SOFABoot's class isolation capability, otherwise, it will be the same as the native `SpringRunner`.

#### SofaJUnit4Runner
Sample code for unit testing:

```java
@RunWith(SofaJUnit4Runner.class)
public class UnitTestCase {

    @Test
    public void test() {
        Assert.assertTrue(true);
    }
}
```

Likewise, `SofaJUnit4Runner` will check whether the dependency is introduced. If so, the `SofaJUnit4Runner` will enable the capability as well, or will be same as the native `JUnit4`.

#### Customized Runner
When writing test cases, sometimes we need to specify a special Runner. For a unified coding style, you can use `SofaBootRunner` and `SofaJUnit4Runner` with the annotation `@DelegateToRunner`. Here is some sample code:

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

It is identical to the following:

```java
@RunWith(BlockJUnit4ClassRunner.class)
public class UnitTestCaseWithoutArk {

    @Test
    public void test() {
        Assert.assertFalse(true);
    }

}
```


