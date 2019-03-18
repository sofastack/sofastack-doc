SOFAArk 的配置目录不是必须存在，如果需要，统一放在工程根目录 `${baseDir}/conf/ark` 下，执行 sofa-ark-maven-plugin 打包，将会自动将该目录下的配置打包至 Ark 包，例如 Ark 包目录为：

```text
.
├── META-INF
│   └── MANIFEST.MF
├── SOFA-ARK
│   ├── biz
│   │   └── demo-0.0.1-SNAPSHOT-ark-biz.jar
│   └── container
│       └── sofa-ark-all-0.6.0-SNAPSHOT.jar
├── com
│   └── alipay
│       └── sofa
│           └── ark
│               ├── ...
│               
└── conf
    └── ark
        ├── bootstrap-dev.properties
        ├── bootstrap.properties
        └── log
            └── logback-conf.xml
```

**注意事项：如果应用中包含 SOFAArk 配置，打包时需要注意 baseDir 配置，用于指定工程根目录，具体[参考文档](./ark-jar.md)**

上述 conf/ark 目录中可以配置 SOFAArk 容器启动配置以及日志配置，下面介绍配置的使用.

conf/ark/bootstrap.properties 是 SOFAArk 容器默认启动配置文件，配置内容包括：日志配置、plugin 激活和钝化配置、biz 激活和钝化配置.

### 日志配置
SOFAArk 容器日志内部实现使用 logback,  日志配置参数包括：
+ logging.path 
> 容器日志目录根路径，这里只影响 SOFAArk 容器日志路径，不影响应用日志，应用自身日志由自身配置决定，默认打印在 ${user.admin}/logs 目录

+ logging.level.com.alipay.sofa.ark
> 设置 SOFAArk 容器日志级别，默认为 INFO

+ logging.config.com.alipay.sofa.ark
> 指定自定义日志配置文件名，用于覆盖 SOFAArk 容器自带的日志配置文件。建议自定义配置文件放在 conf/ark/log 目录中

+ sofa.middleware.log.com.alipay.sofa.ark.console
> 配置容器日志是否打印在 console，默认为 false. 

+ sofa.middleware.log.com.alipay.sofa.ark.console.level
> 配合上述配置项使用，如果打印在 console ，该配置项用于配置 SOFAArk 容器打印在 console 的日志级别

### 插件配置 
+ ark.plugin.active.include
> 指定激活哪些插件，多个插件使用 ',' 分隔；默认激活 Ark 包中所有的插件。

+ ark.plugin.active.exclude
> 指定排除哪些插件，多个插件使用 ',' 分隔；默认为空

**注：如果同时配置了这两个属性，以 ark.plugin.active.include 为准**

### biz配置
+ ark.biz.active.include
> 指定激活哪些 Biz，多个 Biz 使用 ',' 分隔；默认激活 Ark 包中所有的 Biz.

+ ark.biz.active.exclude
> 指定排除哪些 Biz，多个 Biz 使用 ',' 分隔；默认为空

+ com.alipay.sofa.ark.master.biz
> 指定宿主 Biz 名，如果 Ark 包中只有一个 Biz，则不用设置，默认设置为宿主 Biz; 否则需要显示设置

**注：如果同时配置了前两个属性，以 ark.biz.active.include 为准**

### 动态配置
SOFAArk 提供了对接 Zookeeper 的插件，目前用于动态接收 Biz 指令，目前只支持 Zookeeper，配置格式如下：
```text
com.alipay.sofa.ark.config.address=zookeeper://ip:port?key1=value1&key2=value2
```

**特别注意，SOFAArk 有一个默认的逻辑，如果用户配置了 `com.alipay.sofa.ark.config.address`，且 Ark 包中打入了多个 Biz，则只会启动宿主应用(master biz)；这样做的原因是如果配置了动态配置，SOFAArk 会优先根据动态配置控制 Biz 的部署。**

### Profile 机制
默认 SOFAArk 容器使用 bootstrap.properties 配置，实际开发中，可能根据运行环境加载不同的配置，SOFAArk 提供了 profile 机制. 指定 profile 值，SOFAArk 容器会加载 bootstrap-${profile}.properties 配置文件。指定 profile 的配置有两种方式：
+ 通过 -D  VM 参数传入，例如：-Dark.profile=dev,dev2  多个值使用 ',' 隔开。
+ 通过应用启动参数传入，例如：java -jar demo-executable-ark.jar -Aprofile=dev,dev2 多个值使用 ',' 隔开。
