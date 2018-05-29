Spring 框架从 3.1.X 版本开始提供了 profile 功能: [Bean Definition Profiles](https://docs.spring.io/spring/docs/3.1.0.RELEASE/reference/htmlsingle/#d0e1293)，SOFABoot 支持模块级 profile 能力，即在各个模块启动的时候决定模块是否能够启动。

## 使用Module-Profile激活module

使用 SOFABoot 的 profile 功能，需要在 application.properties 文件增加 com.alipay.sofa.boot.active-profiles 字段，该字段的值为逗号分隔的字符串，表示允许激活的 profile 列表，指定该字段后，SOFABoot 会为每个可以激活的模块指定此字段表示的 profile 列表。

SOFABoot 模块的 sofa-module.properties 文件支持 Module-Profile 字段，该字段的值为逗号分隔的字符串，表示当前模块允许在哪些 profile 激活。Module-Profile 支持取反操作， !dev 表示 com.alipay.sofa.boot.active-profiles 不包含 dev 时被激活。

当应用未指定 com.alipay.sofa.boot.active-profiles 参数时，表示应用所有模块均可启动。SOFABoot 模块未指定 Module-Profile 时，表示当前 SOFABoot 模块可以在任何 profile 启动。

## 使用例子

### 激活 dev SOFABoot 模块

application.properties 中增加配置如下：

```properties
com.alipay.sofa.boot.active-profiles=dev
```

该配置表示激活 profile 为 dev 的模块。

在每个需要限定为 dev profile 被激活模块的 sofa-module.properties 文件中增加如下配置：

```properties
Module-Profile=dev
```

### 配置多个激活 profile

application.properties 中增加配置如下：

```properties
com.alipay.sofa.boot.active-profiles=dev,test
```

该配置表示激活 profile 为 dev 或者 test 的模块。

在 SOFABoot 模块的 sofa-module.properties 文件中增加如下配置：

```properties
Module-Profile=test,product
```

该配置表示当 com.alipay.sofa.boot.active-profiles 包含 test 或者 product 时激活模块，由于当前指定的 com.alipay.sofa.boot.active-profiles 为 dev,test ，此模块将被激活。

### Module-Profile 取反

application.properties 中增加配置如下：

```properties
com.alipay.sofa.boot.active-profiles=dev
```

该配置表示激活 profile 为 dev 的模块。

在 SOFABoot 模块的 sofa-module.properties 文件中增加如下配置：

```properties
Module-Profile=!product
```

该配置表示当 com.alipay.sofa.boot.active-profiles 不包含 product 时激活模块，由于当前指定的 com.alipay.sofa.boot.active-profiles 为 dev ，此模块将被激活。

### 设置激活模块 Spring 上下文的 spring.profiles.active 属性

application.properties 中增加配置如下：

```properties
com.alipay.sofa.boot.active-profiles=dev,test
```

该配置表示激活 profile 为 dev 或者 test 的模块，当一个模块满足上面的激活条件时，这个模块就会被启动，同时 Spring 上下文的环境信息 spring.profiles.active 也被设置为了 dev,test ，这样如下的配置 beanId 为 devBeanId 和 testBeanId 的bean都会被激活。

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