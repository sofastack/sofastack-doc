# Introduction
To understand the usage mode of Jarslink2.0, you need to have a certain understanding of the [SOFAArk framework](https://github.com/sofastack/sofa-ark) and [the packaging of Ark packages](../sofa-ark/ark-jar) and [Ark Biz](../sofa-ark/ark-biz) packages.

To ensure the consistency of reading, here is a rough description of the packaging logic of the application's use of Jarslink2.0. The official recommendation is to jump to the above-mentioned link to obtain the necessary background knowledge.

Jarslink2.0 requires an application type of [Spring Boot](https://spring.io/projects/spring-boot) or [SOFABoot](https://github.com/sofastack/sofa-boot). Before introducing new modes of application packaging, let's see why it is necessary for Spring Boot/SOFABoot applications to introduce new packaging modes after using Jarslink2.0.

## Background
At runtime, Jarslink2.0 works as an [Ark Plugin](../sofa-ark/ark-plugin) of the SOFAArk framework, which must be introduced for the use of Jarslink2.0. The Jarslink2.0 plugin will not be loaded and started until the SOFAArk container is started. As we know, when official Spring Boot projects use plugins:
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
```
An executable FatJar will be packaged, which contains all the dependencies, configurations, and other resources required by the running application. The FatJar entry method is the main method of the Spring Boot application. The packaging logic of a SOFABoot project is the same as that of a Spring Boot project.

After the application introduces Jarslink2.0, which needs to depend on the SOFAArk framework, the FatJar that is packaged by a new packaging mode needs to contain a SOFAArk container. The FatJar entry method also needs to be replaced by the SOFAArk container startup method, because the startup of SOFAArk takes precedence over the execution of the FatJar. The SOFAArk container starts all the Ark Plugins in turn before finally starting the application. Therefore, a new packaging mode is needed, and SOFAArk provides a packaging plugin.
```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```
responsible for packaging Spring Boot/SOFABoot applications into an executable FatJar called [Ark package](../sofa-ark/ark-jar).

## Packaging Type
In the previous section, we have described why the use of Jarslink 2.0 needs to introduce a packaging mode that is different from the official Spring Boot packaging mode, and brought out the first packaging type—Ark package. Now let's summarize the features of Ark package:

+ Ark package is an executable FatJar package format customized by the SOFAArk framework, the details of which are available in [Reference Documents](../sofa-ark/ark-jar).
+ The Ark package contains all the configurations, dependencies, and other resources required by the running application. Its packaging logic is similar to that of Spring Boot, and it also contains the Ark Plugin and the SOFAArk framework on which the application depends. The SOFAArk framework does not need to be introduced into the application by the application developers with the traditional label `<dependency/>`, but is automatically packaged by the above-mentioned packaging plugin `sofa-ark-maven-plugin`. The version of SOFAArk framework is that of the packaging plugin.

For easy understanding, let's put it this way: Ark package = Spring Boot FatJar + SOFAArk framework + Ark Plugin. In the new packaging logic, SOFAArk has more rationally reorganized the Spring Boot FatJar into another format, which leads to the application's second packaging type—Biz package. The features of Biz package are summarized as follows:

+ The Biz package is a FatJar package format customized by the SOFAArk framework, the details of which are available in [Reference Documents](../sofa-ark/ark-biz).

+ Similar to the FatJar packaged by the Spring Boot packaging plugin, Biz package contains all the configurations, dependencies, and other resources required by the running application. Note that the only difference is that it cannot run directly with the `java -jar` command.

Therefore, we can actually see it this way: Ark package = Biz package + SOFAArk framework + Ark Plugin.

In conclusion, with the packaging plugin provided by SOFAArk, the application
```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
</plugin>
```
can package two types of packages—Ark package and Biz package.

## Packaging Configuration
Detailed use of the packaging plugin `sofa-ark-maven-plugin` is available in [Reference Documents](../sofa-ark/ark-jar). The following is an attached simple packaging configuration:

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

Note the configuration of "attach", which mainly affects the installation and release of the Biz package. When attach is configured and released as true, execute the mvn install command and the Biz package will be installed in the local maven repository. When the mvn deploy command is executed, the Biz package will also be uploaded to the remote maven repository. The GAV coordinates of the Biz package is determined by the pom.xml file, with the additional attribute of classifier=ark-biz. For the role of classifier, refer to the [StackOverFlow Q&A](https://stackoverflow.com/questions/20909634/what-is-the-purpose-of-mavens-dependency-declarations-classifier-property)


