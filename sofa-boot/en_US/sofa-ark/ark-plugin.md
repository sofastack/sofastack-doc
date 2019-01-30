This section will introduce the standard specifications and directory structure of `Ark Plugin` and  how to use the maven plugin of `sofa-ark-plugin-maven-plugin` to package and release it.

## Plugin Specifications
A standard `Ark Plugin` should meet the following specifications:

* The plugin should have a name (default is `${artifactId}`). At runtime, duplicate names are not allowed. In other words, the name will be used as the unique ID of `Ark Plugin`;

* A plugin must be configured with a priority (default is 1,000): the lower the number, the higher the priority;

* A plugin should be configured with no more than one entry class `activator`, a portal for the container startup plugin used to implement the `com.alipay.sofa.ark.spi.service.PluginActivator` interface class in a uniform way. The plugin with higher priority will start up first;

* Import classes support both package and class levels. They are loaded first from other plugins;

* Export classes support package and class levels. Plugins with higher priority will be exported first;

* Support importing resources from the classpath (wildcard is not supported). Specified resources will be searched for from other plugins first;

* Support exporting resources from the classpath (wildcard is not supported); resources with higher priority will be exported first;


## Maven plugins
The officially provided `Maven` plugin `sofa-ark-plugin-maven-plugin` can package projects into a standard-format `Ark Plugin`. The coordinates of `Maven` plugin are:

```xml
<plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
    <version>${sofa.ark.version}</version>
</plugin>
```

## Goals
The `sofa-ark-plugin-maven-plugin` plugin provides `goal: ark-plugin`, which can be used to package the project into a standard-format `Ark Plugin`. Configurations are as follows:

```xml
<build>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
        <version>${sofa.ark.version}</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>ark-plugin</goal>
                </goals>
            </excution>
        </executions>
        <configuration>
            <!-- Configuration information -->
        </configuration>
    </plugin>
</build>
```

## Complete configuration template

The complete configuration template of the `sofa-ark-plugin-maven-plugin` plugin is shown as follows:

```xml
<plugins>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
        <version>${sofa.ark.version}</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>ark-plugin</goal>
                </goals>

                <configuration>
    
                    <!-- Specify the directory to package ${pluginName}.ark.plugin (${project. build. directory} is the default location) -->
                    <outputDirectory>./</outputDirectory>
    
                    <!-- Specify whether or not to install or publish the Ark plugin to the repository. The default is true.  -->
                    <attach>true</attach>

                    <!-- Ark plugin can specify at most one implementation class for the com.alipay.sofa.ark.spi.service.PluginActivator interface. -->
                    <activator>com.alipay.sofa.ark.service.impl.SampleActivator</activator>

                    <!-- Configure the priority: the smaller the number, the higher the priority for startup and class export (the default is 1000) -->
                    <priority>2000</priority>

                    <!-- Configure the plugin's name. Be sure to make it correct as it will be taken as the only identifier at runtime. The sofa-RPC plugin, for example, can be configued as sofa-RPC. The default is ${artifactId} -->
                    <pluginName>${ark.plugin.name}</pluginName>

                    <!-- Configure imported classes and resources -->
                    <imported>
                        <!-- Configure the packages that need to be loaded from other Ark plugins first -->
                        <packages>
                            <package>javax.servlet</package>
                            <package>org.springframework.*</package>
                        </packages>

                        <!-- Configure the classes that need to be loaded from other Ark plugins first -->
                        <classes>
                            <class>com.alipay.sofa.rpc.config.ProviderConfig</class>
                        </classes>
                        
                        <!-- Specify the resources that need to be loaded from other Ark plugins first -->
                        <resources>
                            <resource>META-INF/spring/bean.xml</resource>>
                        </resources>
                    </imported>

                    <!-- Configure exported classes and resources -->
                    <exported>
                        <!-- Configure exported classes of package level -->
                        <packages>
                            <package>com.alipay.sofa.ark.service</package>
                        </packages>

                        <!-- Configure exported classes of class level -->
                        <classes>
                            <class>com.alipay.sofa.ark.common.CommonUtils</class>
                            <class>com.alipay.sofa.mock</class>
                        </classes>
                        
                        <!-- Configure the resources exported by Ark plugins -->
                        <resources>
                            <resource>META-INF/spring/bean.xml</resource>>
                        </resources>                        
                    </exported>

                    <!-- Exclude specified package dependencies when packaging plugins. The format is:  ${groupId:artifactId} or ${groupId:artifactId:classifier} -->
                    <excludes>
                        <exclude>org.apache.commons:commons-lang3</exclude>
                    </excludes>

                    <!-- Exclude the package dependencies that have the same groupId as specified when packaging plugins -->
                    <excludeGroupIds>
                        <excludeGroupId>org.springframework</excludeGroupId>
                    </excludeGroupIds>

                    <!-- Exclude the package dependencies that have the same artifactId as specified when packaging plugins -->
                    <excludeArtifactIds>
                        <excludeArtifactId>sofa-ark-spi</excludeArtifactId>
                    </excludeArtifactIds>

                </configuration>
            </execution>

        </executions>
    </plugin>
</plugins>
```

The meaning of the configuration items are as follows:
+ `outputDirectory`: Specify the output directory of the ${pluginName}.ark.plugin; ${project.build.directory} is the default location;

+ `attach`: Specify whether to install and publish the Ark plugin to the repository (true by default); 

+ `activator`: Ark plugin can specify at most one implementation class for the `com.alipay.sofa.ark.spi.service.PluginActivator interface`;

+ `priority`: Configure the priority: the smaller the number, the higher the priority for startup and class export (the default is 1000);

+ `pluginName`: Configure the plugin's name. Be sure to make it correct as it will be taken as the only identifier at runtime. For example, the sofa-RPC plugin can be configured as sofa-RPC; the default value is ${artifactId}

+ `imported`: Configure imported classes and resources. Note that when configuring imported classes of the package level, if the package name is suffixed with wildcard `*`, then all packages or classes with that package prefix will be imported. Otherwise, only classes that have the prefix will be imported but its sub-packages will not be imported.

+ `exported`: Configure exported classes and resources. Note that when configuring exported classes of the package level, if the package name is suffixed with wildcard `*`, then all packages or classes with that package prefix will be imported. Otherwise, only classes that have the prefix will be exported but its sub-packages will not be exported.

+ `excludes`: Exclude specified package dependencies when packaging plugins. The format is: ${groupId:artifactId} or ${groupId:artifactId:classifier}; 

+ `excludeGroupIds`: Exclude package dependencies that have the same groupId as specified when packaging plugins; 

+ `excludeArtifactIds`: Exclude package dependencies that have the same artifactId as specified when packaging plugins; 


## Constructing
For a common Java project, generating a standard `Ark Plugin` only needs three steps:
* Create an empty module under the project root directory to package the `Ark Plugin`, and define its coordinates, assuming:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-demo</artifactId>
    <version>1.0.0</version>
</dependency>
```

* Add the third-party dependencies and other modules of the project in the module's pom.xml file, and configure the `sofa-ark-plugin-maven-plugin` plugin in the configuration template; 

* Tap the `mvn package` command under the module directory or the project root directory to generate the `ark-plugin-demo.ark.plugin` file in the configured `outputDirectory` directory. Tap the `mvn install` command to install the file in the local `Maven` repository;


## Publishing
Configure the repository address in the master pom file, and tap the `mvn deploy` command to publish the `Ark plugin`. Note that the coordinates of the `Ark Plugin` will be attached with `classifier = Ark-plugin`. For example, with the above configuration specified, the following coordinates will be generated for the `Ark Plugin`:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-demo</artifactId>
    <classifier>ark-plugin</classifier>
    <version>1.0.0</version>
</dependency>
```

Other applications can reference the plugin via the coordinates.

## Ark Plugin typical directory structure
```text
.
│
├── com
│   └── alipay
│       └── sofa
│           └── ark
│               └── plugin
│                   └── mark
│
├── META-INF
│   └── MANIFEST.MF
│
├── conf
│   └── export.index
└── lib
    ├── commons-lang3-3.3.1.jar
    ├── sample-plugin-1.0.0.jar
    └── sample-plugin-common-1.0.0.jar
```

The files and directories under the above structure are described as follows:

* `com/alipay/sofa/ark/plugin/mark`: A markup file that regards the JAR package as the `Ark Plugin` file generated by the `sofa-Ark-plugin-maven-plugin`.

* `META-INF/MANIFEST.MF`: It is used to record the plugin's meta information, whose content is like the following:
  ```manifest
  Manifest-Version: 1.0
  groupId: com.alipay.sofa
  artifactId: sample-plugin
  version: 1.0.0
  priority: 2000
  pluginName: sample-demo-plugin
  activator: com.alipay.sofa.ark.service.impl.SampleActivator
  import-packages: javax.servlet,org.springframework
  import-classes: com.alipay.sofa.rpc.config.ProviderConfig
  export-packages: com.alipay.sofa.ark.service
  export-classes: com.alipay.sofa.ark.common.CommonUtils,com.alipay.sofa.mock
  ```
  
* `conf/export.index`: An index file of the plugin's exported classes. To avoid computing the exported classes under the `export-packages` at runtime, a corresponding index file will be generated when the Ark Plugin is packaged, thus shortening the time spent in parsing and configuring `Ark Container`. As for imported classes, we only need to load the configurations from the `META-INF/MANIFEST.MF` file and directly compare the prefixes of `import-packages` and `import-classes`, without need to generate an index file.

* `lib/`: The common JAR packages that the project depends on are placed in the lib directory, which generally contains the JAR package whose plugins need to be isolated from other plugins or services. All exported classes of the plugin's configuration are contained in these JAR packages.
