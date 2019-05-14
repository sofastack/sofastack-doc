
> [Project address](https://github.com/sofastack/sofa-ark/tree/master/sofa-ark-samples/sample-ark-plugin)

## Introduction
This sample project demonstrates how to use Maven plugins to package a general Java project into an `Ark plugin` that meets the standard specifications. 

## Background
In actual development, dependency conflicts often occur. Suppose we have developed a class library named `sample-lib`, and it might conflict with the existing dependencies when the business application is imported. At this point, we hope the library can be isolated from other business dependencies, without negotiating with each other over dependency package versions. `Ark Plugin` is exactly the result of our best practice under this demand background. It runs on top of the `Ark Container`, which is loaded by a container. Any `Ark Plugin` is loaded by a separate ClassLoader to be isolated from each other. There are four concepts of `Ark Plugin`:
* Import class: When the plugin starts up, a plugin used to export the class is first delegated to load the class. If that fails, it will attempt to load from inside this plugin;

* Export class: If the class has been imported by other plugins, it will be first loaded from this plugin;

* Import resource: When the plugin is searching for resources, a plugin used to export the class is first delegated to load the class. If that fails, it will attempt to load from inside this plugin.

* Export resource: If the resource has been imported by other plugins, it will be first loaded from this plugin;


> [Refer to the plugin specifications for more details](./ark-plugin)


## Tools
Upon simple configurations, the officially provided `Maven` plugin `sofa-ark-plugin-maven-plugin` can package common Java projects into a standard-format `Ark Plugin`. The coordinates of `Maven` plugin are:

```xml
<plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
    <version>${sofa.ark.version}</version>
</plugin>
```

> [For more information, see the plugin configuration document](./ark-plugin)

## Getting started
Based on this sample project, we will describe how to build an `Ark plugin` step by step. 

### Create a Standard Maven Project
This is a standard Maven project consisting of two modules:
* The common module: contains the plugin export class

* The plugin module: contains `com.alipay.sofa.ark.spi.service.PluginActivator` interface implementation class and a plugin service class. The plugin packaging tool `sofa-ark-plugin-maven-plugin` can be configured in the module's `pom.xml` file;

### Configuring Packaging Plugin
Package the plugin in the `pom.xml` file according to the following configurations:

```xml
<build>
    <plugins>
        <plugin>
            <groupId>com.alipay.sofa</groupId>
            <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
            <version>${project.version}</version>
            <executions>
                <execution>
                    <id>default-cli</id>
                    <goals>
                        <goal>ark-plugin</goal>
                    </goals>

                    <configuration>

                        <!--can only configure no more than one activator-->
                        <activator>com.alipay.sofa.ark.sample.activator.SamplePluginActivator</activator>

                        <!-- configure exported class -->
                        <exported>
                            <!-- configure package-level exported class-->
                            <packages>
                                <package>com.alipay.sofa.ark.sample.common</package>
                            </packages>

                            <!-- configure class-level exported class -->
                            <classes>
                                <class>com.alipay.sofa.ark.sample.facade.SamplePluginService</class>
                            </classes>
                        </exported>

                        <!--specify destination where ark-plugin will be saved, default saved to ${project.build.directory}-->
                        <outputDirectory>../target</outputDirectory>

                    </configuration>
                </execution>

            </executions>
        </plugin>
    </plugins>
</build>
```

In the sample project, we have configured only a fraction of the items, but they are enough to produce an executable `Ark Plugin`. The meaning of each configuration item is shown below:
* activator: The entry class of the Ark container startup plugin. At most one activator can be configured. The plugin's `activator` will generally execute a couple of initializations such as the plugin service publish. In this sample project, the plugin service is published.

* Export package: The package-level export class configuration. All classes inside the plugins prefixed with the name of the export package, including the plugin's third-party dependency packages, will be exported;

* Export class: Configure this to export specified classes with precise class names;

* outputDirectory: The output directory into which the generated Ark plugin is placed after `mvn package` is executed;

Note that in the sample project, we have only exported the classes created by the project. In actual use, however, we can also export the third-party packages that the project depends on.

### Packaging, Installation, Publishing, and Introducing
Like a common project, we can simply use `mvn package`, `mvn install`, or `mvn deploy` to install and publish plugins. Note that the Maven coordinates of `Ark Plugin` that is published by default will add the `classifier=ark-plugin`. In this sample project, if we want to use that ark plugin, we need to configure the dependencies as follows:

```xml
<dependency>
     <groupId>com.alipay.sofa</groupId>
     <artifactId>sample-ark-plugin</artifactId>
     <classifier>ark-plugin</classifier>
     <version>${sofa.ark.version}</version>
 </dependency>
```


