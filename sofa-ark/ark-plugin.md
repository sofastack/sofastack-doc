---
title: Ark Plugin
---

本小节将介绍 `Ark Plugin` 的标准规范和目录结构，以及如何使用官方插件 `sofa-ark-plugin-maven-plugin` 打包发布 `Ark Plugin`。

## 插件规范
标准的 `Ark Plugin` 需要满足以下规范：

* 插件必须配置插件名，默认为 `${artifactId}` ；运行时，不允许存在同名的插件，可以认为它是 `Ark Plugin` 的唯一 ID;

* 插件必须配置优先级，默认为1000，数字越低表示优先级越高；

* 插件最多配置一个入口类 `activator` ，它是容器启动插件的入口，统一实现 `com.alipay.sofa.ark.spi.service.PluginActivator` 接口类；优先级高的插件优先启动；

* 导入类支持 package 和 class 两个级别；导入类优先从其他的插件加载；

* 导出类支持 package 和 class 两个级别；优先级高的插件优先导出；

* 支持导入 classpath 中资源，不支持通配符；优先从其他插件中查找指定资源；

* 支持导出 classpath 中资源，不支持通配符；优先级高的插件优先导出；


## Maven 插件
官方提供 `Maven` 插件 `sofa-ark-plugin-maven-plugin` 可以将工程打包成标准格式的 `Ark Plugin` ； `Maven` 插件坐标为：

```xml
<plugin>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
    <version>0.2.0</version>
</plugin>
```

## Goals
`sofa-ark-plugin-maven-plugin` 插件提供 `goal: ark-plugin`，可以将工程打包成标准格式的 `Ark Plugin`, 如下配置：

```xml
<build>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
        <version>0.2.0</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>ark-plugin</goal>
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

完整的 `sofa-ark-plugin-maven-plugin` 插件配置模板如下：

```xml
<plugins>
    <plugin>
        <groupId>com.alipay.sofa</groupId>
        <artifactId>sofa-ark-plugin-maven-plugin</artifactId>
        <version>0.2.0</version>
        <executions>
            <execution>
                <id>default-cli</id>
                <goals>
                    <goal>ark-plugin</goal>
                </goals>

                <configuration>
    
                    <!-- 指定打包的 ${pluginName}.ark.plugin 存放目录; 默认放在 ${project.build.directory} -->
                    <outputDirectory>./</outputDirectory>
    
                    <!-- 是否把 ark plugin 安装、发布到仓库，默认为true -->
                    <attach>true</attach>

                    <!-- ark plugin 最多仅能指定一个 com.alipay.sofa.ark.spi.service.PluginActivator 接口实现类 -->
                    <activator>com.alipay.sofa.ark.service.impl.SampleActivator</activator>

                    <!-- 配置优先级，数字越小，优先级越高，优先启动，优先导出类，默认1000 -->
                    <priority>2000</priority>

                    <!-- 配置插件的名字，务必配置对，运行时，是插件的唯一标识 ID。比如 sofa-rpc 插件，可以配置为 sofa-rpc; 默认为 ${artifactId} -->
                    <pluginName>${ark.plugin.name}</pluginName>

                    <!-- 配置导入类、资源 -->
                    <imported>
                        <!-- 配置需要优先从其他 ark plugin 加载的 package -->
                        <packages>
                            <package>javax.servlet</package>
                            <package>org.springframework</package>
                        </packages>

                        <!-- 配置需要优先从其他 ark plugin 加载的 class -->
                        <classes>
                            <class>com.alipay.sofa.rpc.config.ProviderConfig</class>
                        </classes>
                        
                        <!-- 配置需要优先从其他 ark plugin 加载的资源 -->
                        <resources>
                            <resource>META-INF/spring/bean.xml</resource>>
                        </resources>
                    </imported>

                    <!-- 配置导出类、资源 -->
                    <exported>
                        <!-- 配置包级别导出的类 -->
                        <packages>
                            <package>com.alipay.sofa.ark.service</package>
                        </packages>

                        <!-- 配置类级别导出类 -->
                        <classes>
                            <class>com.alipay.sofa.ark.common.CommonUtils</class>
                            <class>com.alipay.sofa.mock</class>
                        </classes>
                        
                        <!-- 配置 ark plugin 对外导出的资源 -->
                        <resources>
                            <resource>META-INF/spring/bean.xml</resource>>
                        </resources>                        
                    </exported>

                    <!-- 打包插件时，排除指定的包依赖；格式为: ${groupId:artifactId} 或者 ${groupId:artifactId:classifier} -->
                    <excludes>
                        <exclude>org.apache.commons:commons-lang3</exclude>
                    </excludes>

                    <!-- 打包插件时，排除和指定 groupId 相同的包依赖 -->
                    <excludeGroupIds>
                        <excludeGroupId>org.springframework</excludeGroupId>
                    </excludeGroupIds>

                    <!-- 打包插件时，排除和指定 artifactId 相同的包依赖 -->
                    <excludeArtifactIds>
                        <excludeArtifactId>sofa-ark-spi</excludeArtifactId>
                    </excludeArtifactIds>

                </configuration>
            </execution>

        </executions>
    </plugin>
</plugins>
```

配置项含义如下：
+ `outputDirectory`: 指定打包的 ${pluginName}.ark.plugin 存放目录; 默认放在 ${project.build.directory};

+ `attach`: 是否把 ark plugin 安装、发布到仓库，默认为true; 

+ `activator`: ark plugin 最多仅能指定一个 `com.alipay.sofa.ark.spi.service.PluginActivator` 接口实现类;

+ `priority`: 配置优先级，数字越小，优先级越高，优先启动，优先导出类，默认1000;

+ `pluginName`: 配置插件的名字，务必配置对，运行时，是插件的唯一标识 ID。比如 sofa-rpc 插件，可以配置为 sofa-rpc; 默认为 ${artifactId};

+ `imported`: 配置导入类、资源；

+ `exported`: 配置导出类、资源；

+ `excludes`: 打包插件时，排除指定的包依赖；格式为: ${groupId:artifactId} 或者 ${groupId:artifactId:classifier}；

+ `excludeGroupIds`: 打包插件时，排除和指定 groupId 相同的包依赖；

+ `excludeArtifactIds`: 打包插件时，排除和指定 artifactId 相同的包依赖；


## 构建
对于普通的 Java 工程，为了生成标准的 `Ark Plugin` ，只需要三步操作：
* 在工程根目录中新建一个空模块打包 `Ark Plugin`，定义插件坐标，假设为：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-demo</artifactId>
    <version>1.0.0</version>
</dependency>
```

* 在该模块的 pom.xml 文件中，添加工程其他模块和三方包依赖，并配置模板配置 `sofa-ark-plugin-maven-plugin` 插件；

* 在该模块目录或工程根目录中敲击 `mvn package` 命令，即可在配置的 `outputDirectory` 目录中生成 `ark-plugin-demo.ark.plugin` 插件文件；敲击 `mvn install` 命令，则会安装到本地 `Maven` 仓库；


## 发布
在工程主 pom 中配置仓库地址，然后敲击 `mvn deploy` 命令，即可发布该 `Ark Plugin` ;需要指出的是，发布的 `Ark Plugin` 坐标会带上 `classifier = ark-plugin` ；如上述构建描述，会生成如下坐标的 `Ark Plugin` :

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-ark-plugin-demo</artifactId>
    <classifier>ark-plugin</classifier>
    <version>1.0.0</version>
</dependency>
```

其他应用通过该坐标即可引用该插件。

## Ark Plugin 典型目录结构
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

上述目录结构相关文件和目录说明如下：

* `com/alipay/sofa/ark/plugin/mark` ：标记文件，标记该 Jar 包是 `sofa-ark-plugin-maven-plugin` 打包生成的 `Ark Plugin` 文件。

* `META-INF/MANIFEST.MF` ：记录插件元信息，内容类似如下：
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
  
* `conf/export.index` ：插件导出类索引文件；为了避免在运行时计算插件 `export-packages` 下面具体的导出类，在打包生成 `Ark Plugin` 时，会生成插件所有导出类的索引文件，缩短 `Ark Container` 解析配置时间。而导入类只需要读取 `META-INF/MANIFEST.MF` 配置信息，直接比较 `import-packages` 和 `import-classes` 前缀即可，无需生成索引文件。

* `lib/` : lib 目录存放插件工程依赖的普通 Jar 包，一般包含插件需要和其他插件或者业务有隔离需求的 Jar 包；插件配置的导出类都包含在这些 Jar 包中。



