# SOFABoot

## XML方式

在xml方式中发布和引用服务的方式如下。 sofa:service 元素表示发布服务， sofa:reference 元素表示引用服务。 sofa:binding 表示服务发布或引用的协议。
```xml
<bean id="personServiceImpl" class="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonServiceImpl"/>
<sofa:service ref="personServiceImpl" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt/>
</sofa:service>
```

一个服务也可以通过多种协议进行发布，如下：
```xml
<sofa:service ref="personServiceImpl" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt/>
    <sofa:binding.rest/>
    <sofa:binding.dubbo/>
</sofa:service>
```


服务引用

```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
     <sofa:binding.bolt/>
</sofa:reference>
```

也可以是其他的协议

```xml
<sofa:reference id="personReferenceRest" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
     <sofa:binding.rest/>
</sofa:reference>
```