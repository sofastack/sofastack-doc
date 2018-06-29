SOFABoot 是在 Spring Boot 的基础上提供的功能扩展。基于 Spring Boot 的机制，SOFABoot 管理了 SOFA 中间件的依赖，并且提供了 Spring Boot 的 Starter，方便用户在 Spring Boot 中使用 SOFA 中间件。

## SOFABoot 依赖管理

在使用 SOFA 中间件之前，首先需要在一个现有的 Spring Boot 的工程中添加对应的依赖，有两种方式可以引入 SOFABoot 的依赖管理，一种是通过修改 `<parent>` 的方式，一种是在 `dependencyManagement` 下增加依赖的方式。

修改 `<parent>` 的方式，只需要将原来的 Spring Boot 工程中的 `<parent>` 改成如下的配置即可：

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofaboot.version}</version>
</parent>
```

在 `dependencyManagement` 下增加依赖的方式，只需要将下面的依赖加入到 `dependencyManagement` 下即可：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofaboot.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

**说明: `${sofaboot.version}` 为具体的 SOFABoot 版本，如：`2.4.0`。**

**请注意，当前的 `SOFABoot` 的 `2.4.0` 的版本是基于 `Spring Boot 1.4.2.RELEASE` 来构建的。而大家通过 dependencyManagement 方式添加的 SOFABoot 依赖，只管控了 SOFABoot 相关的版本，由于并未修改 parent Spring Boot 的管控依赖方式，所以 Spring Boot 的实际版本和其相关依赖以具体依赖的 Spring Boot 版本为准。如果遇到兼容问题，请第一时间联系我们。**


## 引入 SOFA 中间件

SOFABoot 使用一系列后缀为 `-sofa-boot-starter` 来标示一个中间件服务，如果想要使用某个中间件，直接添加对应的依赖即可。例如，如果期望使用 SOFARPC，只需增加下面的 Maven 依赖即可：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency>
```

注意上面的 Maven 依赖中并没有声明版本，这个是因为版本已经在 `sofaboot-dependencies` 里面声明好。这样做的好处是对于 SOFA 中间件，用户统一进行升级即可，不需要单独升级一个中间件的版本，防止出现依赖冲突以及兼容性的问题。

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