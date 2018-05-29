# SOFARPC

## 服务发布
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

## 服务引用
服务引用涉及到两个类， `RegistryConfig` 和 `ConsumerConfig` 。
```java
ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())       
            .setRegistry(registryConfig);
HelloService helloService = consumerConfig.refer();
```
ConsumerConfig 表示服务引用，如上声明了所引用服务的接口和服务注册中心。
最终通过 refer 方法将这个服务引用，获取到该服务的远程调用的代理。
