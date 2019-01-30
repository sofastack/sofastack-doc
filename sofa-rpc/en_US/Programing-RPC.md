# SOFARPC

## Service publishing

The process of service publishing involves three classes `RegistryConfig`, `ServerConfig`, `ProviderConfig`.

1. RegistryConfig
```java
RegistryConfig registryConfig = new RegistryConfig()
            .setProtocol("zookeeper")
            .setAddress("127.0.0.1:2181")
```
`RegistryConfig` represents the registry center. As above, the address and port of the service registry center is 127.0.0.1:2181, and the protocol is Zookeeper.

2. ServerConfig
```java
ServerConfig serverConfig = new ServerConfig()
            .setPort(8803)
            .setProtocol("bolt");
```
`ServerConfig` represents the container where service runs. The above declares a server using the 8803 port and the bolt protocol.

3. ProviderConfig
```java
ProviderConfig<HelloWorldService> providerConfig = new ProviderConfig<HelloWorldService>()
            .setInterfaceId(HelloWorldService.class.getName())    
            .setRef(new HelloWorldServiceImpl())    
            .setServer(serverConfig)  
            .setRegistry(registryConfig);
providerConfig.export();
```
`ProviderConfig` represents service publishing. The above declares the interface of the service, implements the server running the service, and eventually publishes the service by the `export` method.

## Service reference
Service reference involves two classes, namely `RegistryConfig` and `ConsumerConfig`.
```java
ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())       
            .setRegistry(registryConfig);
HelloService helloService = consumerConfig.refer();
```
`ConsumerConfig` represents service reference. The above declares the interface and service registry center of the referenced service interface, and finally references the service by the `refer` method to get the proxy for the remote call of the service.