# SOFABoot

## XML mode

The way to publish and reference services in xml mode is as follows. `sofa:service` represents publishing service, and `sofa:reference` represents referencing service. `sofa:binding` indicates the protocol for service publishing or reference.
```xml
<bean id="personServiceImpl" class="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonServiceImpl"/>
<sofa:service ref="personServiceImpl" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt/>
</sofa:service>
```

A service can also be published through multiple protocols, as follows:
```xml
<sofa:service ref="personServiceImpl" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt/>
    <sofa:binding.rest/>
    <sofa:binding.dubbo/>
</sofa:service>
```


Service reference

```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
     <sofa:binding.bolt/>
</sofa:reference>
```

A service can also be referenced through other protocols:

```xml
<sofa:reference id="personReferenceRest" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
     <sofa:binding.rest/>
</sofa:reference>
```