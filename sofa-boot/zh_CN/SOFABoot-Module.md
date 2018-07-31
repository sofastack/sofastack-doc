SOFABoot 模块是一个普通的 Jar 包加上一些 SOFABoot 特有的配置，这些 SOFABoot 特有的配置，让一个 Jar 包能够被 SOFABoot 识别，使之具备模块化的能力。

一个完整的 SOFABoot 模块和一个普通的 Jar 包有两点区别: 

- SOFABoot 模块包含一份 sofa-module.properties 文件，这份文件里面定义了 SOFABoot 模块的名称以及模块之间的依赖关系。
- SOFABoot 模块的 META-INF/spring 目录下，可以放置任意多的 Spring 配置文件，SOFABoot 会自动把它们作为本模块的 Spring 配置加载起来。

## sofa-module.properties 文件详解

先来看一份完整的 sofa-module.properties 文件：

```properties
Module-Name=com.alipay.test.biz.service.impl
Spring-Parent=com.alipay.test.common.dal
Require-Module=com.alipay.test.biz.shared
Module-Profile=dev
```

#### Module-Name

Module-Name 是 SOFABoot 模块的名称，也是 SOFABoot 模块的唯一标示符。在一个 SOFABoot 应用中，一个 SOFABoot 模块的 Module-Name 必须和其他的 SOFABoot 模块的 Module-Name 不一样。需要注意的一点是，一个 SOFABoot 应用运行时的 SOFABoot 模块，不仅仅只包含本应用的模块，还包括依赖了其他应用的 SOFABoot 模块，确定是否唯一的时候需要把这些 SOFABoot 模块也考虑进去。

#### Require-Module

Require-Module 用于定义模块之间的依赖顺序，值是以逗号分隔的 SOFABoot 模块名列表，比如上面的配置中，就表示本模块依赖于 com.alipay.test.biz.shared 模块。对于这种依赖关系的处理，SOFABoot 会将 com.alipay.test.biz.shared 模块在本模块之前启动，即com.alipay.test.biz.shared 模块将先启动 Spring 上下文。

一般情况下，是不需要为模块定义 Require-Module 的，只有当模块的 Spring 上下文的启动依赖于另一个模块的 Spring 上下文的启动时，才需要定义 Require-Module。举一个例子，如果你在 A 模块中发布了一个 SOFA JVM Service。在 B 模块的某一个 Bean 的 init 方法里面，需要使用 SOFA Reference 调用这个 JVM Service。假设 B 模块在 A 模块之前启动了，那么 B 模块的 Bean 就会因为 A 模块的 JVM Service 没有发布而 init 失败，导致 Spring 上下文启动失败。这个时候，我们就可以使用 Require-Module 来强制 A 模块在 B 模块之前启动。

#### Spring-Parent

在 SOFABoot 应用中，每一个 SOFABoot 模块都是一个独立的 Spring 上下文，并且这些 Spring 上下文之间是相互隔离的。虽然这样的模块化方式可以带来诸多好处，但是，在某些场景下还是会有一些不便，这个时候，你可以通过 Spring-Parent 来打通两个 SOFABoot 模块的 Spring 上下文。Spring-Parent 属性可以配置一个模块的名称，比如上面的配置中，就将 com.alipay.test.common.dal 的 Spring 上下文设置为当前模块的 Spring 上下文的父 Spring 上下文。

**由于 Spring 的限制，一个模块的 Spring-Parent 只能有一个模块**

关于 Spring 的父上下文的作用可以看 Spring 的 BeanFactory 的说明：[http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html)

#### Module-Profile

支持 SOFABoot Profile 能力： [SOFABoot Profile](./SOFABoot-Profile)

## Spring 配置文件

SOFABoot 模块可以包含 Spring 配置文件，放置在 META-INF/spring 目录下，SOFABoot 启动时会自动扫描该目录，并把目录下所有 XML 文件作为本模块的 Spring 配置加载起来，我们可以在 Spring 配置文件中定义 Bean、发布服务等等。

SOFABoot 模块一般用于封装对外发布服务接口的具体实现，属于业务层，Controller 属于展现层内容，我们不建议也不支持在 SOFABoot 模块中定义 Controller 组件，Controller 组件相关定义建议直接放在 Root Application Context。
