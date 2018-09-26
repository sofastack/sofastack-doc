SOFABoot 是在 Spring Boot 的基础上提供的功能扩展。基于 Spring Boot 的机制，SOFABoot 管理了 SOFA 中间件的依赖，并且提供了 Spring Boot 的 Starter，方便用户在 Spring Boot 中使用 SOFA 中间件。

## SOFABoot 依赖管理

在使用 SOFA 中间件之前，需要引入 SOFABoot 依赖管理。类似 Spring Boot 引入方式，在工程中增加如下 `<parent/>` 标签配置的方式:

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofa.boot.version}</version>
</parent>
```
其中 `${sofa.boot.version}` 为具体的 SOFABoot 版本，参考[发布历史](https://github.com/alipay/sofa-boot/releases)。


## 引入 SOFA 中间件

SOFABoot 使用一系列后缀为 `-sofa-boot-starter` 来标示一个中间件组件，如果想要使用某个中间件，直接添加对应的依赖即可。例如，如果期望使用 SOFARPC，只需增加下面的 Maven 依赖即可：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency>
```

注意上面的 Maven 依赖中并没有声明版本，这个是因为版本已经在 `sofaboot-dependencies` 里面声明好。这样做的好处是对于 SOFA 中间件，用户统一进行升级即可，不需要单独升级一个中间件的版本，防止出现依赖冲突以及兼容性的问题。目前管控的 SOFABoot 中间件列表如下:

|中间件|starter|文档地址|
|:---:|:---:|:---:|
|SOFARPC|rpc-sofa-boot-starter||
|SOFATracer|tracer-sofa-boot-starter|s|
|SOFALookout|lookout-sofa-boot-starter|s|

## 引入 SOFABoot 扩展组件
SOFABoot 基于 Spring Boot 提供了健康检查，模块隔离，类隔离等扩展能力。遵循 Spring Boot 依赖即服务的理念，添加相关组件依赖之后，扩展能力即可生效。目前提供的扩展组件如下：

|扩展组件|starter|文档地址|
|:---:|:---:|:---:|
|健康检查|healthcheck-sofa-boot-starter||
|模块化隔离|isle-sofa-boot-starter|s|
|类隔离|sofa-ark-springboot-starter|s|
|测试扩展|test-sofa-boot-starter||

## 引入 SOFA 中间件 ark 插件 
SOFABoot 提供了类隔离组件 [SOFAArk](./sofa-ark/readme)，借助 SOFAArk 容器，用户可以将依赖冲突的三方包打包成 ark 插件。运行时，ark 插件使用单独的类加载器加载，可以和其他 ark 插件以及业务依赖隔离，解决类冲突问题。SOFABoot 官方提供了 SOFARPC 和 SOFATracer 的 ark 插件，例如在应用中引入 SOFARPC ark 插件依赖替代 SOFARPC starter，从而隔离应用和 SOFARPC 及其间接依赖。目前管控的 ark 插件列表如下:

|Ark插件|plugin|文档地址|
|:---:|:---:|:---:|
|SOFARPC|rpc-sofa-boot-plugin||
|SOFATracer|tracer-sofa-boot-plugin||

## 引入 SOFABoot 命名空间

使用 SOFA 中间件时，需要在 `XML` 中根据中间件的具体使用方式添加相应的配置，这个时候需要引入 SOFABoot 的命名空间 `xmlns:sofa="http://sofastack.io/schema/sofaboot"` 以能够正确解析相应的配置标签，示例：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:sofa="http://sofastack.io/schema/sofaboot"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
       default-autowire="byName">
</beans>
```