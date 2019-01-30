SOFABoot supports modular isolation. But in actual usage scenarios, There is one case that beans in one module sometimes need to open some entries for another module to expand. SOFABoot draws on and uses the [Nuxeo Runtime] (https://github.com/nuxeo-archives/nuxeo-runtime) project and the [nuxeo](https://github.com/nuxeo/nuxeo) project and expands on it, provides the ability to extend points with Spring, We call it `Extension Point`.

## Usage

Using extension point capabilities in SOFABoot requires the following three steps:

### Define a bean that provides extension capabilities

When using the SOFABoot extension point capability, you first need to define an interface that needs to be extended, like:

```java
package com.alipay.sofa.boot.test;

public interface IExtension {

    String say();
}
```

Define the implementation of this interface:

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

Here you can see that there is a method: `registerExtension`, you can temporarily ignore this method, and later will introduce its specific role.

In the module's Spring configuration file, we add configuration of this bean:

```xml
<bean id="extension" class="com.alipay.sofa.boot.test.impl.ExtensionImpl">
    <property name="word" value="Hello, world"/>
</bean>
```

### Defining extension points

There is a field word in the above bean. In practice, we want this field to be overridden by other module customizations. Here we expose it as an extension point.

First, you need a class to describe this extension point:

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

Then define the extension point in xml:

```xml
<sofa:extension-point name="word" ref="extension">
    <sofa:object class="com.alipay.sofa.boot.test.extension.ExtensionDescriptor"/>
</sofa:extension-point>
```

among them:
- `name` is the name of the extension point
- `ref` is the bean to which the extension point is applied
- `object` is a concrete description of the contribution point of the extension point. This description is done by XMap (XMap is used to map Java objects and XML files. It is recommended to search XMap documents on the Internet to understand XMap)

### Defining extension implements

The above has defined the extension point, and we can extend this bean at this point:

```xml
<sofa:extension bean="extension" point="word">
    <sofa:content>
        <word>
            <value>newValue</value>
        </word>
    </sofa:content>
</sofa:extension>
```

among them:
- `bean` is the bean to which the extension is applied
- `point` is the name of the extension point
- The content inside `content` is an extension definition, which will parse the content through XMap into: the extension point's contribution point specific description object, here is the `com.alipay.sofa.boot.test.extension.ExtensionDescriptor` object

At this point, we can look back at the `registerExtension` method defined in `com.alipay.sofa.boot.test.impl.ExtensionImpl`. When SOFABoot resolves to the contribution point, it will call the `registerExtension of the extended bean. The ` method contains the user-defined contribution point processing logic. In the above example, the user-defined value is obtained and set to the value in the word field that overrides the original definition in the bean.

At this point, call the `extension` bean's `say()` method, and you can see the value defined in the return extension: newValue.

## XMap Support and Extension

The above example is just a very simple extension. In fact, XMap contains a very rich description capability, including `List`, `Map`, etc. These can be seen by looking at the XMap documentation.

In SOFABoot, in addition to XMap native support, it extends the ability to integrate with Spring:
- Extend `XNodeSpring` via `XNode`
- Extend `XNodeListSpring` via `XNodeList`
- Extend `XNodeMapSpring` via `XNodeMap`

This part of the expansion capabilities, the ability to extend the extension point, the description object can directly point to a SpringBean (user configuration bean name, SOFABoot will get the bean from the spring context according to the name), here is a use of `XNodeListSpring` The example is still the three steps described above:

### Defining a bean that provides extended capabilities

In this interface definition, we return a list, the goal is that this list can be filled by extension:

```java
package com.alipay.sofa.boot.test;

public interface IExtension {

    List<SimpleSpringListBean> getSimpleSpringListBeans();
}
```

Where `SimpleSpringListBean` can be defined according to requirements, here we assume that an empty return is defined.

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

You can see that the bean defined by the extension point is added to the list in `registerExtension` to achieve the ability for the user to extend the list.

In the module's Spring configuration file, we configure this bean:

```xml
    <bean id="iExtension" class="com.alipay.sofa.runtime.integration.extension.bean.IExtensionImpl"/>
```

###  Defining extension points

First, you need an object to describe:

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

Here we use `@XNodeListSpring`. When the name of the corresponding bean is configured in XML, SOFABoot gets the corresponding bean instance from the spring context.

Define this extension point in XML

```xml
<sofa:extension-point name="testSpringList" ref="iExtension">
        <sofa:object class="com.alipay.sofa.runtime.integration.extension.descriptor.SpringListExtensionDescriptor"/>
    </sofa:extension-point>
```

### Defining extensions

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

When defining an extension, first define two beans and then put them into the extension definition.

At this point, call `iExtension.getSimpleSpringListBeans` method, you can see that it contains two beans added by the extension.

## Defining extension points and extensions by client mode

In addition to defining extension points and extensions through the above xml configuration, SOFABoot also provides `com.alipay.sofa.runtime.api.client.ExtensionClient` to define extension points and extensions.

### Get `ExtensionClient`

Getting `com.alipay.sofa.runtime.api.client.ExtensionClient` is relatively simple, and `com.alipay.sofa.runtime.api.aware.ExtensionClientAware` is provided in SOFABoot:

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

Define `ExtensionClientBean` as a Spring bean.

### 定义扩展点

```java
ExtensionPointParam extensionPointParam = new ExtensionPointParam();
extensionPointParam.setName("clientValue");
extensionPointParam.setTargetName("iExtension");
extensionPointParam.setTarget(iExtension);
extensionPointParam.setContributionClass(ClientExtensionDescriptor.class);
extensionClient.publishExtensionPoint(extensionPointParam);
```

The meaning of each parameter is consistent when the client and the extension point are registered via XML:
among them:
- `name` is the name of the extension point
- `targetName` is the name of the bean to which the extension point is applied
- `target` is the bean to which the extension point is applied
- `contributionClass` is a concrete description of the contribution points of the extension point. The definitions described here are as follows:

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

Define extension points by `publishExtensionPoint` of `com.alipay.sofa.runtime.api.client.ExtensionClient`

### Defining extension implements

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

The meaning of each parameter is consistent when the client and the extension point are registered via XML:
- `targetInstanceName` is the bean to which the extension is applied
- `targetName` is the name of the extension point
- The content inside the `element` is an extended definition. Here you need to pass in the `Element` object. By reading it from XML, the contents of the example are as follows:

```xml
<clientValue>
    <value>SOFABoot Extension Client Test</value>
</clientValue>
```

The extension can be defined by `com.alipay.sofa.runtime.api.client.ExtensionClient.publishExtension()`

### Restrictions

It can be seen that since the definition of the extension is strongly dependent on XML, although the extension point and extension are released through the client, the content of the extension itself needs XML to describe, and does not really use only the client. This part is welcome to contribute.