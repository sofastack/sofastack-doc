# About SOFABoot

SOFABoot is a development framework open sourced by Ant Financial which is based on Spring Boot, provides capabilities such as Readiness Check, class isolation, and log space isolation. In addition to enhancing the Spring Boot, SOFABoot provides users with the capability to easily use SOFA middleware in Spring Boot.

You can view all the release notes in [Release History](https://github.com/sofastack/sofa-boot/releases). The correspondence between SOFABoot version and Spring Boot version is as follows:

|SOFABoot version| Spring Boot version|
|:---:|:---:|
|2.3.x|1.4.2.RELEASE|
|2.4.x|1.4.2.RELEASE|
|2.5.x|1.5.16.RELEASE|
|3.0.x|2.0.3.RELEASE|
|3.1.0|2.1.0.RELEASE|

That is, the SOFABoot 2.3.x and 2.4.x series are based on Spring Boot 1.4.2.RELEASE; SOFABoot 2.5.x series are based on Spring Boot 1.5.x; SOFABoot 3.x series are based on Spring Boot 2.x. You can view and get the codes of all revisions in [Release History](https://github.com/sofastack/sofa-boot/releases). In addition, to facilitate users in the community to learn the latest development version of SOFABoot, we will release the SNAPSHOT version, which is a branch of the current development. To successfully pull the SNAPSHOT package from the central repository, it's necessary to add the following profile configuration to the local maven setting.xml file:

```xml
<profile>
    <id>default</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <repositories>
        <repository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>maven-snapshot</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
        </pluginRepository>
    </pluginRepositories>
</profile>
```

## Feature Description

Based on Spring Boot, SOFABoot provides the following capabilities:

* Capability of expanding the Health Check of Spring Boot: Provide the Readiness Check based on the Health Check of Spring Boot, to ensure a secure launch of application examples.
* Capability of log space isolation: The middleware framework automatically finds the application's logs and realizes dependence on the logs and independent log printing, avoiding binding the middleware and the application logs. The capability is achieved through [sofa-common-tools](https://github.com/sofastack/sofa-common-tools).
* Capability of providing class isolation: Provide class isolation based on the [SOFAArk](https://github.com/sofastack/sofa-Ark) framework, making it easy for users to solve various class conflicts.
* Capability of providing modular development: Based on the Spring context isolation, provide [modular development](./Modular-Development) capability, with a separate Spring context for each SOFABoot module, to avoid BeanId conflicts between different SOFABoot modules.
* Integrated management of middleware: manage in a unified manner, provide a unified and easy-to-use programming interface for middleware, and each SOFA middleware is an independent and pluggable component.
* Fully compatible with Spring Boot: SOFABoot is built based on and is fully compatible with Spring Boot.

## Application Scenarios

SOFABoot itself is based on Ant Financial's Spring Boot practices and makes up for some deficiencies of Spring Boot in large-scale financial production scenarios, so it is especially suitable for such scenarios.

Of course, each component of SOFABoot is optional, and users may flexibly select the functions. For example, if a user only wants to introduce SOFA middleware under Spring Boot, it's not necessary to introduce the capability of class isolation in SOFABoot.
