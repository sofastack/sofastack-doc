# SOFABoot

具体方式见 [sofa-boot-starter 用户手册](https://github.com/alipay/sofa-rpc-boot-projects/wiki/UserGuide)

## XML方式

在xml方式中发布和引用服务的方式如下。 sofa:service 元素表示发布服务， sofa:reference 元素表示引用服务。 sofa:binding 表示服务发布或引用的协议。
```xml
<bean id="personServiceImpl" class="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonServiceImpl"/>
<sofa:service ref="personServiceImpl" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt/>
</sofa:service>
```
```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
     <sofa:binding.bolt/>
</sofa:reference>
```
## 注解方式 

注解方式的编程界面会在后续版本开发，如果你能参与贡献就更好了。