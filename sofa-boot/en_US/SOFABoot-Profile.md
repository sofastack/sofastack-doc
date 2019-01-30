Since 3.1.X Spring framework has started to support the profile function: [Bean Definition Profiles](https://docs.spring.io/spring/docs/3.1.0.RELEASE/reference/htmlsingle/#d0e1293), SOFABoot support modular-level profiling, it will determine whether a module can be started when each module is getting started.

## Activating Module Using Module-Profile

To enable the SOFABoot profiling, we need to add the com.alipay.sofa.boot.active-profiles field in the application.properties file. The value of this field is a comma-separated string denoting a list of profiles allowed to be activated. After specifying it, SOFABoot will specify a profile list represented by the field for each module that can be activated.

The sofa-module.properties file of the SOFABoot module supports the Module-Profile field, which points to a comma-separated string of values representing which profiles are allowed to be activated. Module-Profile supports the inversion operation, !dev indicates that com.alipay.sofa.boot.active-profiles is activated when it does not contain dev.

If the value of the com.alipay.sofa.boot.active-profiles field is not specified in the application, all modules are allowed to be started. If the Module-Profile is not specified in the SOFABoot module, the current SOFABoot module can be started with any profile.

## Example

### Activating the dev SOFABoot Module

Add the following configurations to the application.properties file:

```properties
com.alipay.sofa.boot.active-profiles=dev
```

With this configuration, the module with dev profile will be activated.

Add the following configuration to each sofa-module.properties file where modules with dev profile need to be activated.

```properties
Module-Profile=dev
```

### Configuring Multiple Activation Profiles

Add the following configurations to the application.properties file:

```properties
com.alipay.sofa.boot.active-profiles=dev,test
```

With this configuration, the modules with dev or test profile will be activated.

Add the following configuration to the SOFABoot's sofa-module.properties file:

```properties
Module-Profile=test,product
```

With this configuration, the module will be activated when the com.alipay.sofa.boot.active-profiles contains test or product. Since the com.alipay.sofa.boot.active-profiles is specified as dev and test, this module will be activated.

### The Inverted Module-Profile

Add the following configurations to the application.properties file:

```properties
com.alipay.sofa.boot.active-profiles=dev
```

With this configuration, the module with dev profile will be activated.

Add the following configuration to the SOFABoot's sofa-module.properties file:

```properties
Module-Profile=!product
```

This will activate the module when the com.alipay.sofa.boot.active-profiles does not contain product. Since it is specified as dev, this module will be activated.

### Set the spring.profiles.active property that is used to activate the Spring context of the module.

Add the following configurations to the application.properties file:

```properties
com.alipay.sofa.boot.active-profiles=dev,test
```

With this configuration, the modules with dev or test profile will be activated. If a module meets those conditions, it will be started and the profile of Spring context will be set to dev,test. In this way, the following xml configuration will create devBeanId and testBeanId at the same time.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xmlns:jee="http://www.springframework.org/schema/jee"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                                                http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                                                http://www.springframework.org/schema/context
                                                http://www.springframework.org/schema/context/spring-context.xsd"
       default-autowire="byName">       

    <beans profile="dev">
        <bean id="devBeanId" class="com.alipay.cloudenginetest.sofaprofilesenv.DemoBean">
            <property name="name">
                <value>demoBeanDev</value>
            </property>
        </bean>
    </beans>

    <beans profile="test">
        <bean id="testBeanId" class="com.alipay.cloudenginetest.sofaprofilesenv.DemoBean">
            <property name="name">
                <value>demoBeanTest</value>
            </property>
        </bean>
    </beans>
</beans>
```
