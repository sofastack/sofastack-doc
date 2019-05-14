# 快速入门

本文档将演示了如何在 SOFABoot 环境下应用 SOFARPC 进行服务的发布和引用。 

您可以直接在工程下找到本文档的[示例代码](https://github.com/sofastack/sofa-rpc-boot-projects/tree/master/sofa-boot-samples)。注意,示例代码中需要本地安装 zookeeper 环境,如果没有安装.需要将`application.properties`中的`com.alipay.sofa.rpc.registry.address` 配置注释掉.走本地文件注册中心的方式

## 创建工程
1. 环境准备：SOFABoot 需要 JDK7 或者 JDK8 ，需要采用 Apache Maven 2.2.5 或者以上的版本来编译。
2. 工程构建：SOFABoot 构建在 Spring Boot 之上。因此可以使用 [Spring Boot 的工程生成工具 ](http://start.spring.io/)来生成一个标准的Spring Boot 工程。
3. 引入 SOFABoot 环境：生成的 Spring Boot 标准工程直接使用的 Spring Boot 的 parent 依赖，改为 SOFABoot 提供的 parent 依赖，该parent 提供并管控了多种 SOFABoot 提供的 starter。
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/> 
</parent>
```
  替换为：
```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>3.0.0</version>
</parent>
```
4. 配置 application.properties ：
application.properties 是 SOFABoot 工程中的配置文件。这里需要配置一个必不可少的配置项，即应用名。
```
spring.application.name=AppName
```
5. 引入 RPC Starter：
```xml
<dependency>
     <groupId>com.alipay.sofa</groupId>
     <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency>
```
6. 声明 SOFABoot 的 xsd 文件：
  在要使用的 XML 配置文件中将头部 xsd 文件的声明设置为如下。这样就能够使用 SOFABoot 定义的 XML 元素进行开发。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:sofa="http://sofastack.io/schema/sofaboot"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://sofastack.io/schema/sofaboot   http://sofastack.io/schema/sofaboot.xsd"
       default-autowire="byName">
```

## 定义服务接口与实现
```java
public interface HelloSyncService {

    String saySync(String string);
}
```
```java
public class HelloSyncServiceImpl implements HelloSyncService {

    @Override
    public String saySync(String string) {
        return string;
    }
}
```
## 服务端发布服务
在 xml 文件中编写如下配置。Spring 上下文在刷新时，SOFABoot 就将该服务实现注册到了服务器上，以 bolt 协议与客户端进行通信地址，并将地址等元数据发布到了注册中心(这里默认使用的本地文件作为注册中心)。
```xml
<bean id="helloSyncServiceImpl" class="com.alipay.sofa.rpc.samples.invoke.HelloSyncServiceImpl"/>
<sofa:service ref="helloSyncServiceImpl" interface="com.alipay.sofa.rpc.samples.invoke.HelloSyncService">
    <sofa:binding.bolt/>
</sofa:service>
```

## 客户端引用服务
在 xml 文件中编写如下配置。Spring 上下文刷新时，SOFABoot 会生成一个RPC的代理 bean，即 personReferenceBolt 。这样就可以直接在代码中使用该 bean 进行远程调用了。

```xml
<sofa:reference id="helloSyncServiceReference" interface="com.alipay.sofa.rpc.samples.invoke.HelloSyncService">
    <sofa:binding.bolt/>
</sofa:reference>
```
## 运行
在 SpringBoot 的启动类中编码如下。其中利用 ImportResource 将上述的xml文件加载。
```java
@ImportResource({ "classpath*:rpc-sofa-boot-starter-samples.xml" })
@org.springframework.boot.autoconfigure.SpringBootApplication
public class SofaBootRpcSamplesApplication {
    public static void main(String[] args) {
          SpringApplication springApplication = new SpringApplication(SofaBootRpcSamplesApplication.class);
          ApplicationContext applicationContext = springApplication.run(args);          

          HelloSyncService helloSyncServiceReference = (HelloSyncService) applicationContext
            .getBean("helloSyncServiceReference");

          System.out.println(helloSyncServiceReference.saySync("sync"));
    }
}
```
打印结果如下：

```
sync
```
以上就完成了一次服务发布和服务引用。
