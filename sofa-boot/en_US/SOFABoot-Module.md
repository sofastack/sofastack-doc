The SOFABoot module combines a regular JAR with some SOFABoot-specific configurations, which enables a JAR to be identified by SOFABoot and modularized.

There are two differences between a complete SOFABoot module and a regular JAR: 

- A SOFABoot module contains a sofa-module.properties file, where the name and the dependencies of the module are defined.
- We can place one or more Spring configuration files in the SOFABoot module's META-INF/spring directory; and SOFABoot will automatically load them as Spring configurations for that module.

# Inside the sofa-module.properties file

Let's look at a complete sofa-module.properties file:

```properties
Module-Name=com.alipay.test.biz.service.impl
Spring-Parent=com.alipay.test.common.dal
Require-Module=com.alipay.test.biz.shared
Module-Profile=dev
```

#### Module-Name

This is the name of a SOFABoot module, which is also the unique identifier of the module. In a SOFABoot application, the Module-Name of a SOFABoot module must be different from that of another SOFABoot module. Note that the SOFABoot modules of a SOFABoot application runtime do not only cover the modules of the current application but also the modules introduced by dependency from other applications. When determining whether a module is unique, you must take these SOFABoot modules into consideration.

#### Require-Module

This defines the dependency order of different modules. The value contains a comma-separated list of SOFABoot module names. For example, in the preceding configuration, it indicates that the current module depends on the com.alipay.test.biz.shared module. The SOFABoot framework processes this dependency by starting the com.alipay.test.biz.shared module before the current module. 

In most cases, you do not have to define the Require-Module for a module. It is required only when the startup of a module's Spring context depends on that of another module's. For example, you have published a JVM Service in module A. In the init method of a Bean in module B, you need to call the JVM Service with a SOFA Reference. Assume that module B is started before module A, the Bean of module B will fail because the JVM Service of module A is not published yet and the init method fails. In this case, you can use the Require-Module to force module A to start before module B.

#### Spring-Parent

In a SOFABoot application, each SOFABoot module has a separate Spring context, and these Spring contexts are isolated from each other. Although this modular approach has many benefits, it can still cause some inconveniences in certain scenarios. For these scenarios, you can use the Spring-Parent to connect the Spring contexts of two SOFABoot modules. The name of a module can be configured with the Spring-Parent property. For example, in the preceding configuration, the Spring context of com.alipay.test.common.dal is set to the parent of the current module's Spring context.


** Due to Spring's limitations, a module's Spring-Parent contains only one module**

As to the role of Spring parent context, please refer to Spring's BeanFactory description:[http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html)

#### Module-Profile

Support SOFABoot Profile: [SOFABootProfile](./SOFABoot-Profile)

## Spring Configuration Files

The SOFABoot module can contain Spring configuration files that have to be placed under the META-INF/spring directory. Once the SOFABoot is started, it will automatically scan the directory and load all the XML files as the Spring configuration of that module. In Spring configuration files, we can define beans and publish services.

The SOFABoot module is used to encapsulate the implementation of the service and thus belongs to the business layer; whereas the Controller belongs to the presentation layer. We do not recommend or support defining Controller components in the SOFABoot module. Relevant definitions should be placed in the Root Application Context.

