> [Project address](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/spring-boot-transform-sample)

## Introduction
Jarslink 2.0 is available for both Spring Boot and SOFABoot; we just need to add the specified dependencies. To be convenient, it is recommended to use Jarslink 2.0 in the form of SOFABoot projects. This sample project is intended to demonstrate how to quickly reform a Spring Boot project into a SOFABoot project.

## Reform
After creating a Spring Boot project in the [official Spring Boot website](https://start.spring.io/), we only need to introduce the SOFABoot dependencies. First, modify the configuration file pom.xml of the Maven project.
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/> 
</parent>
```
Replace as
```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>2.5.0-SNAPSHOT</version>
</parent>
```

**Note: Currently Jarslink 2.0.0 is still at its snapshot version, and the SOFABoot 2.5.0 that it depends on will be released in the near future, so for the moment, the SOFABoot 2.5.0-SNAPSHOT version has to be introduced as the dependency. The pull of the SNAPSHOT package requires special configuration, for which you can refer to [FAQ:  How do I configure for pulling a SNAPSHOT dependency package?](./faq)**

Then, add a Spring Boot or SOFABoot official Starter, such as:
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
To package the application into an Ark package or Biz package, we need to configure the sofa-Ark-maven-plugin packaging plugin in the main pom.xml file, and delete the native packaging plugin the Spring Boot configuration.
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
Finally, add a parameter that SOFABoot must use under the application.properties file for the project, including spring.application.name (used to mark the name of the current application) and logging path (used to specify the log output directory).
```text
# Application Name
spring.application.name=spring-boot-transform-sample

# logging path
logging.path=./logs
```

In this way, a common Spring Boot project has been reformed into a SOFABoot project and integrated the Jarslink 2.0 component.

### Run the application
The output directory in the packaging plugin has been configured as ./target, and after running the mvn clean install command in the application's root directory, you will find that the Ark package and Biz package that the application has packaged have been generated under the ./target directory, as follows:
+ spring-boot-transform-sample-${version}-executable-Ark.jar; this is an Ark package.
+ spring-boot-transform-sample-${version}-Ark-biz.jar; this is a Biz package.

We can start this Ark package with the use of java -jar. For more information about packaging, refer to [Application Packaging](./jarslink-repackage).
