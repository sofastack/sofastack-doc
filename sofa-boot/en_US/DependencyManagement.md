SOFABoot is based on Spring Boot. It means SOFABoot manages SOFA middleware dependencies and provides the Starter for Spring Boot, facilitating the use of SOFA middleware in Spring Boot.

## SOFABoot dependency management

You must load SOFABoot's management dependencies before using SOFA middleware. In a way similar to use Spring Boot, add the configuration tag `<parent/>` in the project settings:

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofa.boot.version}</version>
</parent>
```
Where `${sofa.boot.version}` represents the SOFABoot version (refer to [release history](https://github.com/sofastack/sofa-boot/releases)).


## Use Middleware of SOFAStack

For SOFABoot, use `-sofa-boot-starter` suffixes to name middleware components. If you want to use middleware, simply add its dependencies; To use SOFARPC, for example, simply add the following Maven dependencies:

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>rpc-sofa-boot-starter</artifactId>
</dependency>
```

Note that there is no version declaration in the above Maven dependencies as the version has already been declared in `sofabook-dependencies`. This allows for unified upgrade of all SOFA middleware versions, allaying concerns over dependency conflicts or incompatibility brought by upgrade of a single middleware version. The SOFABoot middleware under control is listed as follows:

| Middleware | starter |
|:---:|:---:|
|SOFARPC|rpc-sofa-boot-starter|
|SOFATracer|tracer-sofa-boot-starter|
|SOFALookout|lookout-sofa-boot-starter|

## Introducing SOFABoot Extension
Based on Spring Boot, SOFABoot provides extended capabilities such as health check, module isolation, and class isolation. In accordance with Spring Boot's the dependency-as-a-service principle, the extension capability will be ready immediately after relevant dependencies are added. Currently, there are several extension modules available:

| Extension components | starter |
|:---:|:---:|
| Health check | healthcheck-sofa-boot-starter |
| Module isolation | Isle-Sofa-boot-starter |
| Class isolation | sofa-Ark-springboot-starter |
| Test extension | test-Sofa-boot-starter |

## Introducing the SOFA middleware: the Ark plug-in 
SOFABoot provides a class isolation component—[SOFAArk](./sofa-Ark/readme), which enables users to package third-party packages with dependency conflicts into an Ark plug-in. At run time, the Ark plug-in is loaded with a separate classloader; it is isolated from other Ark plug-ins and business dependencies to address class conflicts. SOFABoot provides SOFARPC and SOFATracer's Ark plug-ins; the Ark plug-in SOFARPC, for example, is loaded into the application to replace SOFARPC starter, to isolate the application from SOFARPC and its indirect dependencies. The controlled Ark plug-ins are listed as follows:

| Ark plug-in | plugin |
|:---:|:---:|
|SOFARPC|rpc-sofa-boot-plugin|
|SOFATracer|tracer-sofa-boot-plugin|

## Introducing SOFABoot namespace

Before using SOFA middleware, we need to add relevant configurations in `XML` according to specific middleware usage. At this point, we need to introduce the SOFABoot namespace `xmlns:sofa="http://sofastack.io/schema/sofaboot"` so that the corresponding configuration labels can be correctly parsed. For example:

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
