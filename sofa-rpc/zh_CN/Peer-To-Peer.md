## 直连调用
SOFARPC 支持指定地址进行调用的场景。使用方式如下，设置直连地址即可。
```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()        
            .setInterfaceId(HelloService.class.getName())        
            .setRegistry(registryConfig)        
            .setDirectUrl("bolt://127.0.0.1:12201");
```
