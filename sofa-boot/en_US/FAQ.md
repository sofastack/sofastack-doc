### Q: Which application scenarios does Readiness Check have?

The concepts of Liveness Check and Readiness Check come from Kuberentes, representing run-time check and startup check respectively. Spring Boot supports Liveness Check, but does not support Readiness Check. With Readiness Check, all components of SOFA middleware can introduce traffic to application instances only after passing the Readiness Check. For example, only after passing the Readiness Check, RPC will register with the service registration center, and then the traffic flow from the upstream applications can enter. In addition to the traffic control by Readiness Check events from the middleware, the PAAS system can obtain the status of the Readiness Check via http://localhost:8080/health/readiness to control the traffic of, among others, the load balancing equipment.

### Q: Can I define a controller component in a SOFABoot module?
The SOFABoot module is generally used to encapsulate the implementation of publishing service interfaces, which belongs to the business layer; whereas Controller belongs to the presentation layer. Defining Controller in the SOFABoot module is not recommended and therefore not supported. The Controller definition is recommended to be put in the Root Application Context.

### Q: Is class isolation widely used at Ant Financial?

Yes. Most of the business applications are run on the class isolation framework based on the middleware developed by Ant Financial. The goal is to resolve dependency conflicts. Even tech giants like Ant Financial, which has complex business lines and numerous underlying service components, find it hard to manage all jars in a unified manner. Especially for inter-dependent modules developed across teams, it is difficult to effectively unify conflicting packages due to their respective historical technology stacks. Class isolation solves many problems in development. Business developers no longer need to worry about their dependency conflicts and they also enjoy great advantages in collaborative development.

### Q: What are the differences between SOFABoot class isolation framework (SOFAArk) and OSGI container?

As highly reputed dynamic module systems in the open source community, OSGi-based Equinox and Felix feature class isolation capabilities as well. However, they focus more on the programming model and the modular development. Besides, they have a full set of module lifecycle management, definition module communication mechanisms, and complex class-loading models. As an isolation framework focusing on resolving dependency conflicts, SOFAArk is dedicated to class isolation to simplify the class-loading model, making the framework more lightweight. Moreover, according to OSGi specifications, all modules are defined as Bundles. Application developers need to understand how the OSGi works, which is a demanding task. In SOFAArk, by contrast, two layers of modules—Ark plugin and Ark Paz—are defined. Application developers only need to add isolated Ark plugin dependencies, leaving the underlying class-loading model transparent to application developers, which brings little extra learning cost.

### Q: What are the differences between SOFAArk modularity and Java 9 modularity?
As a solution to Java 9 modularity, Jigsaw is quite similar to OSGi in use specifications regardless of its implementation details, but several concepts—such as module dependency, package import and export, dynamic export, readability transfer, module service registration and consumption, open module, and optional module—are more complex compared with the simple package import and export of SOFAArk. For JDK compatibility, Jigsaw doesn't support class isolation, so different modules can still be loaded by the same classloader. Strictly speaking, Jigsaw hasn't solved the one-class-multiple-versions problem. With dependency declarations in modules, the conflict can either be detected at compile time or cause boot failure in the pure Jigsaw modular programming mode, because different modules are not allowed to have packages with the same class name. A class of cases is encountered in development. For example, two components rely on different versions of the Hessian package. Even if the two components are defined as two modules, only one version of the Hessian package is loaded at runtime. So different versions still cannot co-exist. In addition, compared with Ark and OSGi, Jigsaw has a major drawback, that is, it does not allow dynamic release of module services at runtime. To communicate between modules, you must use "provides" and "uses" clauses to statically register and reference module services in module-info.java. Of course, Jigsaw has many advantages. First, by introducing module-path to declare module dependencies, it avoids the class loading uncertainty at runtime caused by indirect dependence in the traditional maven/gradle; second, it separates the interface from implementation for enhanced security by configuring module package import and export; third, with the Java 9 Jlink tool, developers can aggregate the required modules of their applications and create a custom JRE image.

### Q: Why can't the SNAPSHOT version be used to pull dependency?
To pull dependency in the SNAPSHOT version, you can operate in two ways: 1. Pull the sofa-Ark repository code and execute the 'mvn install' command locally; 2. Add the following profile configuration to the local Maven setting.xml file.
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

### Q: Why does the application automatically exit when I use java-jar to start Spring Boot/SOFABoot to apply the Ark package?
The SOFAArk container does not run any non-daemon threads. So for a non-Web application or an application that starts without creating a non-daemon thread, the application will exit normally when the "main" method is executed. To verify whether the Ark package has started properly, we can check if the following logs appear:
```text
Ark container started in xxx ms.
```

### Q: What's the difference between SOFABoot isle module and SOFAArk？
SOFABoot module and SOFAArk are two completely unrelated concepts. SOFABoot module is part of the application and is a Java module form defined by SOFABoot. You can refer to [the reference document] (https://www.sofastack.tech/sofa-boot/docs/SOFABoot-Module). Each SOFABoot module is a separate application context after application start-up, but loaded by the same ClassLoader. More details [see article] (https://www.sofastack.tech/posts/2018-07-25-01). Simply put, SOFABoot module is a modular development scheme provided by SOFABoot. SOFAArk is a more standardized business component development framework, which provides class isolation, dynamic deployment and other capabilities. In theory, it can be integrated into any type of Java applications. In SOFAArk, biz and plugin are the models defined by SOFAArk. Each Biz is an application. It can be either Spring Boot or SOFABoot, or even ordinary Java application. At runtime, each Biz is loaded by a separate ClassLoader; the SOFABoot module and the Biz in SOFArk are two different dimensions of the model relationship. Perhaps only one thing is similar. They all interact through the same programming interface (@SofaService/@SofaReference).