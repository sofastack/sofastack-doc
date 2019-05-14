# Quick Start

This document introduces how to use SOFARPC for service publishing and reference in SOFABoot. 

You can get the code sample of this document by clicking [here](https://github.com/sofastack/sofa-rpc/tree/master/example/src/test/java/com/alipay/sofa/rpc/quickstart). Note that the code sample requires a local installation of the zookeeper environment. If not, you need to remove the `com.alipay.sofa.rpc.registry.address` configuration in `application.properties` to use the local file as a registry center.

## Create a project 

1. Prepare environment: SOFABoot requires JDK7 or JDK8 and needs to be compiled with Apache Maven 2.2.5 or above. 
2. Build SOFABoot project: SOFABoot is based on Spring Boot. So you can use [Spring Boot's project generation tool](http://start.spring.io/) to generate a standard Spring Boot project. 
3. Add SOFABoot dependency: The generated standard Spring Boot project directly uses Spring parent dependency, which should be changed to the parent dependency provided by SOFABoot. The parent dependency provides and manages a variety of starters provided by SOFABoot. 

```xml 
<parent> 
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId> 
    <version>${spring.boot.version}</version> 
    <relativePath/> 
</parent> 
```

Replace the above with the followings: 

```xml 
<parent> 
    <groupId>com.alipay.sofa</groupId> 
    <artifactId>sofaboot-dependencies</artifactId> 
    <version>3.0.0</version> 
</parent> 
``` 

4. Configure `application.properties`:
`application.properties` is the configuration file in SOFABoot project. Here you need to configure the application name. 

``` 
spring.application.name=AppName 
``` 

5. Introduce RPC starter:

```xml 
<dependency>
     <groupId>com.alipay.sofa</groupId>
     <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency> 
``` 

6. Declare the xsd file of SOFABoot:

In the XML configuration file to be used, configure the declaration of the header xsd file to the followings. This enables development using the XML elements defined by SOFABoot. 

```xml 
<?xml version="1.0" encoding="UTF-8"?> 
<beans xmlns="http://www.springframework.org/schema/beans" 
       xmlns:xsi="http://www .w3.org/2001/XMLSchema-instance" 
       xmlns:sofa="http://sofastack.io/schema/sofaboot" 
       xmlns:context="http://www.springframework.org/schema/context" 
       xsi:schemaLocation ="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd 
            http://sofastack.io/schema/sofaboot http://sofastack .io/schema/sofaboot.xsd"
```

## Define service interface and implementation 

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

## Publish service on server
Configure the followings in the xml file. When the Spring context is refreshed, SOFABoot registers the service implementation on the server, communicates with the client by bolt protocol, and publishes metadata such as address to registry center (local file is used as registry center by default). 

```xml 
<bean id="helloSyncServiceImpl" class="com.alipay.sofa.rpc.samples.invoke.HelloSyncServiceImpl"/> 
<sofa:service ref="helloSyncServiceImpl" interface="com.alipay.sofa.rpc. samples.invoke.HelloSyncService"
    <sofa:binding.bolt/> 
</sofa:service> 
``` 

## Reference service by client
Configure the followings in the xml file. When the Spring context is refreshed, SOFABoot generates an RPC proxy bean,       `personReferenceBolt`. This allows you to use the bean directly in the code for remote call. 

```xml 
<sofa:reference id="helloSyncServiceReference" interface="com.alipay.sofa.rpc.samples.invoke.HelloSyncService"> 
    <sofa:binding.bolt/> 
</sofa:reference> 
``` 

## Run the project
The startup class of SpringBoot is coded as follows. The above xml file is loaded here using ImportResource. 

```java 
@ImportResource({ "classpath*:rpc-sofa-boot-starter-samples.xml" }) 
@org.springframework.boot.autoconfigure.SpringBootApplication
public class SofaBootRpcSamplesApplication {
    public static void main(String[] args) {
          SpringApplication springApplication = new SpringApplication(SofaBootRpcSamplesApplication.class); 
          ApplicationContext applicationContext = springApplication.run(args);           

          HelloSyncService helloSyncServiceReference = (HelloSyncService) applicationContext 
            .getBean("helloSyncServiceReference"); 

          System.out.println(helloSyncServiceReference.saySync("sync") ); 
    } 
} 
``` 

The output result is as follows: 

``` 
sync 
``` 

By this step, you've completed service publishing and reference.