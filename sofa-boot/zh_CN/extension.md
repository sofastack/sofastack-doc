SOFABoot 支持模块化隔离，在实际的使用场景中，一个模块中的 bean 有时候需要开放一些入口，供另外一个模块扩展。SOFABoot 借鉴和使用了 [Nuxeo Runtime](https://github.com/nuxeo-archives/nuxeo-runtime) 项目 以及 [nuxeo](https://github.com/nuxeo/nuxeo) 项目，并在上面扩展，与 Spring 融合，提供扩展点的能力。

## 使用

在 SOFABoot 中使用扩展点能力，需要以下三个步骤：

### 定义提供扩展能力的 bean

在使用 SOFABoot 扩展点能力时，首先需要定一个需要被扩展的 bean，先定一个接口：

```java
package com.alipay.sofa.boot.test;

public interface IExtension {

    String say();
}
```

定义这个接口的实现：

```java
package com.alipay.sofa.boot.test.impl;

public class ExtensionImpl implements IExtension {

    private String word;

    @Override
    public String say() {
        return word;
    }

    public void setWord(String word) {
        this.word = word;
    }

    public void registerExtension(Extension extension) throws Exception {
        Object[] contributions = extension.getContributions();
        String extensionPoint = extension.getExtensionPoint();

        if (contributions == null) {
            return;
        }

        for (Object contribution : contributions) {
            if ("word".equals(extensionPoint)) {
                setWord(((ExtensionDescriptor) contribution).getValue());
            }
        }
    }
}
```

在这里可以看到有一个方法：`registerExtension` ，暂时可以先不用管这个方法，后续会介绍其具体的作用。

在模块的 Spring 配置文件中，我们把这个 bean 给配置起来：

```xml
<bean id="extension" class="com.alipay.sofa.boot.test.impl.ExtensionImpl">
    <property name="word" value="Hello, world"/>
</bean>
```

### 定义扩展点

在上面的 bean 中有一个字段 word ，在实际中，我们希望这个字段能够被其他的模块自定义进行覆盖，这里我们将其以扩展点的形式暴露出来。

首先需要一个类去描述这个扩展点：

```java
@XObject("word")
public class ExtensionDescriptor {

    @XNode("value")
    private String value;

    public String getValue() {
        return value;
    }
}
```

然后在 xml 中定义扩展点：

```xml
<sofa:extension-point name="word" ref="extension">
    <sofa:object class="com.alipay.sofa.boot.test.extension.ExtensionDescriptor"/>
</sofa:extension-point>
```

其中：
- name 为扩展点的名字
- ref 为扩展点所作用在的 bean
- object 为扩展点的贡献点具体的描述，这个描述是通过 XMap 的方式来进行的(XMap 的作用是将 Java 对象和 XML 文件进行映射，这里建议通过在网上搜索下 XMap 的文档来了解 XMap)

### 定义扩展

上述已经将扩展点定义好了，此时我们就可以对这个 bean 进行扩展了:

```xml
<sofa:extension bean="extension" point="word">
    <sofa:content>
        <word>
            <value>newValue</value>
        </word>
    </sofa:content>
</sofa:extension>
```

其中：
- bean 为扩展所作用在的 bean
- point 为扩展点的名字
- content 里面的内容为扩展的定义，其会通过 XMap 将内容解析为：扩展点的贡献点具体的描述对象，在这里即为 `com.alipay.sofa.boot.test.extension.ExtensionDescriptor` 对象

到这里，我们可以回头看一开始在 `com.alipay.sofa.boot.test.impl.ExtensionImpl` 中定义的 `registerExtension` 方法了，SOFABoot 在解析到贡献点时，会调用被扩展 bean 的 `registerExtension` 方法，其中包含了用户定义的贡献点处理逻辑，在上述的例子中，获取用户定义的 value 值，并将其设置到 word 字段中覆盖 bean 中原始定义的值。

此时，调用 `extension` bean 的 `say()` 方法，可以看到返回扩展中定义的值: newValue 。

## XMap 支持和扩展

上述的例子中只是一个很简单的扩展，其实 XMap 包含了非常丰富的描述能力，包括 `List`, `Map` 等，这些可以通过查看 XMap 的文档来了解。

在 SOFABoot 中，除了 XMap 原生的支持以外，还扩展了跟 Spring 集成的能力：
- 通过 `XNode` 扩展出了 `XNodeSpring`
- 通过 `XNodeList` 扩展出了 `XNodeListSpring`
- 通过 `XNodeMap` 扩展出了 `XNodeMapSpring`

这部分的扩展能力，让扩展点的能力更加丰富，描述对象中可以直接指向一个 SpringBean(用户配置 bean 的名字，SOFABoot 会根据名字从 spring 上下文中获取到 bean)，这里举一个使用 `XNodeListSpring` 的例子，依然是上述描述的三个步骤：

### 定义提供扩展能力的 bean

接口定义：

在这个接口里，返回一个 list，目标是这个 list 能够被通过扩展的方式填充

```java
package com.alipay.sofa.boot.test;

public interface IExtension {

    List<SimpleSpringListBean> getSimpleSpringListBeans();
}
```

其中 `SimpleSpringListBean` 可根据需求来定义，这里我们假设定义了一个空实现

实现：

```java
public class IExtensionImpl implements IExtension {
    private List<SimpleSpringListBean>       simpleSpringListBeans  = new ArrayList<>();

    @Override
    public List<SimpleSpringListBean> getSimpleSpringListBeans() {
        return simpleSpringListBeans;
    }
    
     public void registerExtension(Extension extension) throws Exception {
        Object[] contributions = extension.getContributions();
        String extensionPoint = extension.getExtensionPoint();

        if (contributions == null) {
            return;
        }

        for (Object contribution : contributions) {
            if ("testSpringList".equals(extensionPoint)) {
                simpleSpringListBeans.addAll(((SpringListExtensionDescriptor) contribution)
                    .getValues());
            }
        }
    }
}
```

可以看到：在 `registerExtension` 中将通过贡献点定义的 bean 加入到 list 中，以达到让用户扩展这个 list 的能力。

在模块的 Spring 配置文件中，我们把这个 bean 给配置起来：

```xml
    <bean id="iExtension" class="com.alipay.sofa.runtime.integration.extension.bean.IExtensionImpl"/>
```

### 定义扩展点

首先需要一个对象去描述：

```java
@XObject("testSpringList")
public class SpringListExtensionDescriptor {

    @XNodeListSpring(value = "value", componentType = SimpleSpringListBean.class, type = ArrayList.class)
    private List<SimpleSpringListBean> values;

    public List<SimpleSpringListBean> getValues() {
        return values;
    }
}
```

在这里用到了 `@XNodeListSpring` ，当在 xml 中配置相应 bean 的名字时， SOFABoot 会从 spring 上下文中获取到相应的 bean 实例

在 xml 中将这个扩展点定义出来

```xml
<sofa:extension-point name="testSpringList" ref="iExtension">
        <sofa:object class="com.alipay.sofa.runtime.integration.extension.descriptor.SpringListExtensionDescriptor"/>
    </sofa:extension-point>
```

### 定义扩展

```xml
<bean id="simpleSpringListBean1" class="com.alipay.sofa.runtime.integration.extension.bean.SimpleSpringListBean" />
<bean id="simpleSpringListBean2" class="com.alipay.sofa.runtime.integration.extension.bean.SimpleSpringListBean" />

<sofa:extension bean="iExtension" point="testSpringList">
    <sofa:content>
        <testSpringList>
            <value>simpleSpringListBean1</value>
            <value>simpleSpringListBean2</value>
        </testSpringList>
    </sofa:content>
</sofa:extension>
```

在定义扩展时，首先定义了两个 bean，然后将它们放入扩展定义中。 

此时，调用 `iExtension` 的 `getSimpleSpringListBeans` 方法，可以看到其中包含了通过扩展方式添加的两个 bean。


## 通过客户端方式定义扩展点和扩展

除了通过上述 xml 的方式配置定义扩展点和扩展以外，SOFABoot 中还提供了 `com.alipay.sofa.runtime.api.client.ExtensionClient` 的方式来定义扩展点和扩展

### 获取 `ExtensionClient`

获取 `com.alipay.sofa.runtime.api.client.ExtensionClient` 比较简单，SOFABoot 中提供了 `com.alipay.sofa.runtime.api.aware.ExtensionClientAware`：

```java
public class ExtensionClientBean implements ExtensionClientAware {
 
     private ExtensionClient extensionClient;
 
     @Override
     public void setExtensionClient(ExtensionClient extensionClient) {
         this.clientFactory = extensionClient;
     }
 
     public ExtensionClient getClientFactory() {
         return extensionClient;
     }
}
```

将 `ExtensionClientBean` 定义为 spring bean 即可

### 定义扩展点

```java
ExtensionPointParam extensionPointParam = new ExtensionPointParam();
extensionPointParam.setName("clientValue");
extensionPointParam.setTargetName("iExtension");
extensionPointParam.setTarget(iExtension);
extensionPointParam.setContributionClass(ClientExtensionDescriptor.class);
extensionClient.publishExtensionPoint(extensionPointParam);
```

通过客户端和通过 xml 注册扩展点时各参数的含义保持一致：
其中：
- name 为扩展点的名字
- targetName 为扩展点所作用在的 bean 的名字
- target 为扩展点所作用在的 bean
- contributionClass 为扩展点的贡献点具体的描述，这里描述的定义示例如下：

```java
@XObject("clientValue")
public class ClientExtensionDescriptor {
    @XNode("value")
    private String value;

    public String getValue() {
        return value;
    }
}
```

通过 `com.alipay.sofa.runtime.api.client.ExtensionClient` 的 `publishExtensionPoint` 即可定义扩展点

### 定义扩展

```java
DocumentBuilderFactory dbFactory = DocumentBuilderFactory.newInstance();
DocumentBuilder dBuilder = dbFactory.newDocumentBuilder();
Document doc = dBuilder.parse(new File(Thread.currentThread().getContextClassLoader()
    .getResource("META-INF/extension/extension.xml").toURI()));
ExtensionParam extensionParam = new ExtensionParam();
extensionParam.setTargetName("clientValue");
extensionParam.setTargetInstanceName("iExtension");
extensionParam.setElement(doc.getDocumentElement());
extensionClient.publishExtension(extensionParam);
```

通过客户端和通过 xml 注册扩展点时各参数的含义保持一致：
- targetInstanceName 为扩展所作用在的 bean
- targetName 为扩展点的名字
- element 里面的内容为扩展的定义，这里需要传入 `Element` 对象，通过从 xml 中读取，这里示例子的内容如下：

```xml
<clientValue>
    <value>SOFABoot Extension Client Test</value>
</clientValue>
```

通过 `com.alipay.sofa.runtime.api.client.ExtensionClient` 的 `publishExtension` 即可定义扩展

### 客户端限制

可以看到由于扩展的定义强依赖 XML ，故而虽然这里是通过客户端发布扩展点和扩展，但是扩展自身的内容还是需要 XML 来描述，并没有真正做到只使用客户端，此部分欢迎有兴趣的同学提改进意见。