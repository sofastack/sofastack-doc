SOFABoot 提供了模块并行加载以及 Spring Bean 异步初始化能力，用于加快应用启动速度。模块并行加载参考[相应文档](./parallel-start)，下面介绍如何使用 SOFABoot 异步初始化 Spring Bean 能力来提高应用启动速度。

## 引入依赖
SOFABoot 在 v2.6.0 开始提供异步初始化 Spring Bean 能力，引入如下 Starter 即可：

```xml  
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>
```

## 使用场景
在实际使用 Spring/Spring Boot 开发中，会有一些 Bean 在初始化过程中执行准备操作，如拉取远程配置、初始化数据源等等；在应用启动期间，这类 Bean 会增加 Spring 上下文刷新时间，导致应用启动耗时变长。为了加速应用启动，SOFABoot 通过配置可选项，将 Bean 的初始化方法(init-method) 使用单独线程异步执行，加快 Spring 上下文加载过程，提高应用启动速度。

## 使用方法
异步初始化 Bean 的原理是开启单独线程负责执行 Bean 的初始化方法(init-method)，因此在使用过程中，除了引入上述依赖管理，还需要在 Bean 的 xml 定义中配置 `async-init="true"` 属性，用于指定是否异步执行该 Bean 的初始化方法，例如：

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:sofa="http://sofastack.io/schema/sofaboot"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
       default-autowire="byName">
    <!-- async init  test -->
    <bean id="testBean" class="com.alipay.sofa.runtime.beans.TimeWasteBean" init-method="init" async-init="true"/>
</beans>
```

## 配置
SOFABoot 异步初始化能力提供两个属性配置，用于指定负责异步执行 Bean 初始化方法(init-method)的线程池大小：
+ com.alipay.sofa.boot.asyncInitBeanCoreSize
> 线程池基本大小，默认值为 CPU 核数加一
+ com.alipay.sofa.boot.asyncInitBeanMaxSize
> 线程池中允许的最大线程数大小，默认值为 CPU 核数加一

配置可以通过 VM -D 参数或者 Spring Boot 配置文件 application.yml 设置。