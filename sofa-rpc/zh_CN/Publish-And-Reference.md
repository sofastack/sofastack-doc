## 服务发布/服务引用
服务发布与引用如前文编程界面所示。其中一些特性如下：

* 同一服务发布多种协议
如下构建多个 ServerConfig 设置给 ProviderConfig 。在 SOFABoot 环境中配置多个 binding 即可。
```java
 List<ServerConfig> serverConfigs = new ArrayList<ServerConfig>();
 serverConfigs.add(serverConfigA);
 serverConfigs.add(serverConfigB);
 
 providerConfig.setServer(serverConfigs);
```
```xml
<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt/>
    <sofa:binding.rest/>
    <sofa:binding.dubbo/>
</sofa:service>
```

* 同一服务注册多个注册中心。
如下构建多个 RegistryConfig 设置给 ProviderConfig 。
```java
List<RegistryConfig> registryConfigs = new ArrayList<RegistryConfig>();
registryConfigs.add(registryA);
registryConfigs.add(registryB);

providerConfig.setRegistry(registryConfigs);
```

* 方法级参数设置。
API 方式中，使用相应的对象 set 方法即可为其设置参数。特别说明的是 SOFARPC 提供了 MethodConfig 进行方法级别的参数设置，如下：
```java
MethodConfig methodConfigA = new MethodConfig();
MethodConfig methodConfigB = new MethodConfig();

List<MethodConfig> methodConfigs = new ArrayList<MethodConfig>();
methodConfigs.add(methodConfigA);
methodConfigs.add(methodConfigB);
        
providerConfig.setMethods(methodConfigs);  //服务端设置
consumerConfig.setMethods(methodConfigs);  //客户端设置
```
* 在 SOFABoot 环境中设置参数：
```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000" address-wait-time="2000"/>  <!-- 调用超时；地址等待时间。 -->
        <sofa:route target-url="127.0.0.1:22000"/>  <!-- 直连地址 -->
        <sofa:method name="sayName" timeout="3000"/> <!-- 方法级别配置 -->
    </sofa:binding.bolt>
</sofa:reference>

<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000"/>
        <sofa:method name="sayName" timeout="2000"/>
    </sofa:binding.bolt>
</sofa:service>
```