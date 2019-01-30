This section will introduce the directory structure of standard `Ark package` and how to use the maven plugin of `sofa-Ark-maven-plugin` to package and release an `Ark package`.

## Maven plugin
The officially provided `Maven` plugin `sofa-Ark-maven-plugin` can package common Java projects or Spring Boot projects into standard-format `Ark packages`. Based on [Fat Jar] (https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html#executable-jar-jar-file-structure) technology, we can directly start an `Ark package` with the `java -jar` command. The `Maven` plugin coordinates are:

```xml
 <plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-maven-plugin</artifactId>
    <version>${sofa.ark.version}</version>
</plugin>
```

## Goals
The `sofa-Ark-maven-plugin` plugin provides `goal: repackage`, which can package the project into an executable `Ark package`, it can be configured as follows:

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
            <!-- Configuration information -->
        </configuration>
    </plugin>
</build>
```
## Complete configuration template
Complete `sofa-Ark-maven-plugin` configuration template is as follows:

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

                    <!--The default packaging storage directory for Ark package and Ark biz is the project build directory-->
                    <outputDirectory>../target</outputDirectory>

                    <!--The name of the generated Ark package file is ${artifactId} by default-->
                    <finalName>demo-ark</finalName>

                    <!--Whether to skip execution of goal:repackage (false by default)-->
                    <skip>false</skip>

                    <!--Whether to package, install, and release Ark biz (false by default). Refer to the Ark Biz file for details -->
                    <attach>true</attach>

                    <!--Set the classifier of Ark package, which is null by default-->
                    <arkClassifier>ark-classifier</arkClassifier>

                    <!-- Set the classifier of Ark biz, which is Ark-biz by default -->
                    <bizClassifier>ark-biz-classifier</bizClassifier>

                    <!--Exclude the specified package dependency when packaging the Ark biz. The format is: ${groupId:artifactId} or  ${groupId:artifactId:classifier}-->
                    <excludes>
                        <exclude>org.apache.commons:commons-lang3</exclude>
                    </excludes>

                    <!--Exclude the package dependency that is the same as the specified groupId when packaging the Ark biz-->
                    <excludeGroupIds>
                        <excludeGroupId>org.springframework</excludeGroupId>
                    </excludeGroupIds>

                    <!--Exclude the package dependency that is the same as the specified artifactId when packaging the Ark biz-->
                    <excludeArtifactIds>
                        <excludeArtifactId>sofa-ark-spi</excludeArtifactId>
                    </excludeArtifactIds>
                    
                    <!--Configure the classes that are not indexed from the Ark plugin when packaging the Ark biz. Ark biz will prioritize indexing the export classes of all the Ark plugins by default.
                    After this configuration is added, Ark biz will load these classes only within itself, instead of prioritizing delegating the Ark plugin to load--> 
                    <denyImportClasses>
                        <class>com.alipay.sofa.SampleClass1</class>
                        <class>com.alipay.sofa.SampleClass2</class>
                    </denyImportClasses>
                    
                    <!--Corresponding to denyImportClasses configuration, the package level can be configured-->
                    <denyImportPackages>
                        <package>com.alipay.sofa</package>
                        <package>org.springframework.*</package>
                    </denyImportPackages>
                    
                    <!-- Configure resources that are not indexed from the Ark plugin when packaging the Ark biz. Ark biz will prioritize indexing the export resources of all the Ark plugins by default.
                    After this configuration is added, Ark biz will search for these resources only within itself, instead of searching in the Ark plugin-->
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
The meaning of the configuration items is as follows:

+ `outputDirectory`: Ark package and Ark biz, which is the project build directory by default;

+ `finalName`: generated Ark package file, which is ${artifactId} by default;

+ `skip`: whether to skip the execution of goal:repackage, which is false by default;

+ `attach`: whether to package, install, and release the Ark biz (refer to the Ark Biz file for details), which is false by default;

+ `arkClassifier`: the classifier of the set Ark package, which is null by default;

+ `bizClassifier`: the classifier of the set Ark biz, which is Ark-biz by default;

+ `excludes`: exclude the specified package dependency when packaging the Ark biz. The format is: ${groupId:artifactId} or ${groupId:artifactId:classifier};

+ `excludeGroupIds`: exclude the package dependency that is the same as the specified groupId when packaging the Ark biz;
+ `excludeArtifactIds`: exclude the package dependency that is the same as the specified artifactId when packaging the Ark biz;

+ `denyImportClasses`: configure the classes that are not indexed from the Ark plugin when packaging the Ark biz. Ark biz will prioritize indexing the exported classes of all the Ark plugins by default. After this configuration is added, Ark biz will only load these classes within itself, instead of prioritizing delegating the Ark plugin to load;

+ `denyImportPackages`: corresponding to denyImportClasses configuration, the package level can be configured. Note that if there is a wildcard `*` at the end of the package name, the sub-packages prefixed with this package name will all be denied. If there is no wildcard `*` at the end of the package name, only those classes prefixed with the package name will be denied.

+ `denyImportResources`: Configure the resources that are not indexed from the Ark plugin when packaging the Ark biz. Ark biz will prioritize indexing the export resources of all the Ark plugins by default. After this configuration is added, Ark biz will only search for these resources within itself, instead of searching in the Ark plugin

## Constructing
Take the Spring Boot Web project as an example, it takes only two steps to generate a standard `Ark package`
* With reference to the configuration template, configure the `sofa-Ark-maven-plugin` plugin in the pom.xml file of the Web module;
* Tap the `mvn pacakge` command in the Web module directory or the application root directory to generate `Ark package` and `Ark Biz` file in the `outputDirectory`, respectively named Web module `${artifactId}` and `${artifactId}-Ark-biz` by default. You can also set the classifier with `arkClassifier` and `bizClassifier` respectively. Tap the `mvn install` command to install the `Ark package` in the local repository, and install the "Ark biz" in the local repository if `attach` is set to `true`;
  
## Releasing
Configure the repository address in the main pom file of project, tap the `mvn deploy` command to release the `Ark package`. Note that if `attach` is set to `true`, `Ark Biz` will also be released to the repository. In addition, the released `Ark Biz` package will have `classifier = Ark-biz` with it by default, and the classifier of `Ark biz` can be set with `bizClassifier`;

## Typical Directory Structure of Ark Package
The following is the directory structure of an `Ark package` generated by packaging of a common `Spring Boot Web` application

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

The files and directories under the above directory structure are described as follows:

* `META-INF/MANIFEST.MF`: Records the meta information about the `Ark` package, with the most critical piece being the startup entry class: `Main-Class`, whose content is as follows:

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
As you can see, the entry class of the `ark package` is `com.alipay.sofa.ark.bootstrap.ArkLauncher`

* `com/alipay/sofa/ark/*`: Stores the boot classes required to start up the `Ark package` by executing the `java -jar` command;

* `SOFA-ARK/container`: Stores the `ark container` dependency packages (actually the directory where the following dependency packages will be unzipped);

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-all</artifactId>
    <version>${sofa.ark.version}</version>
</dependency>
```

* `SOFA-ARK/biz`: Stores all the `Ark Biz` packages (here resides a single `Ark Biz` because the demo project does not depend on any other `Ark Biz` packages)

* `SOFA-ARK/plugin`: Contains all the `Ark Plugin` packages that the application depends on. The demo project only depends on the RPC `Ark Plugin`
