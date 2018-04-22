# API
### 服务发布
  服务发布过程涉及到三个类 `RegistryConfig` ，`ServerConfig` ，`ProviderConfig` 。

1. RegistryConfig
```java
RegistryConfig registryConfig = new RegistryConfig()
            .setProtocol("zookeeper")
            .setAddress("127.0.0.1:2181")
```
RegistryConfig 表示注册中心。如上声明了服务注册中心的地址和端口是127.0.0.1:2181，协议是 Zookeeper 。

2. ServerConfig
```java
ServerConfig serverConfig = new ServerConfig()
            .setPort(8803)
            .setProtocol("bolt");
```
ServerConfig 表示服务运行容器。如上声明了一个使用8803端口和 bolt 协议的 server 。

3. ProviderConfig
```java
ProviderConfig<HelloWorldService> providerConfig = new ProviderConfig<HelloWorldService>()
            .setInterfaceId(HelloWorldService.class.getName())    
            .setRef(new HelloWorldServiceImpl())    
            .setServer(serverConfig)  
            .setRegistry(registryConfig);
providerConfig.export();
```
ProviderConfig 表示服务发布。如上声明了服务的接口，实现和该服务运行的 server 。
最终通过 export 方法将这个服务发布出去了。

### 服务引用
服务引用涉及到两个类， `RegistryConfig` 和 `ConsumerConfig` 。
```java
ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())       
            .setRegistry(registryConfig);
HelloService helloService = consumerConfig.refer();
```
ConsumerConfig 表示服务引用，如上声明了所引用服务的接口和服务注册中心。
最终通过 refer 方法将这个服务引用，获取到该服务的远程调用的代理。

# SOFABoot
具体方式见 [sofa-boot-starter 用户手册](https://github.com/alipay/sofa-rpc-boot-projects/wiki/UserGuide)
### XML
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
### Annotation 
注解方式的编程界面会在后续版本开发，如果你能参与贡献就更好了。