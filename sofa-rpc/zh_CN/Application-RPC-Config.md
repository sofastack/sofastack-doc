在 SOFABoot 的使用场景下，RPC 框架在应用层面，提供一些配置参数，支持的应用级别的参数配置，如端口，线程池等信息，都是通过
Spring Boot的`@ConfigurationProperties` 进行的绑定。绑定属性类是`com.alipay.sofa.rpc.boot.config.SofaBootRpcProperties`，

以 Bolt 相关参数为例，在这个类中是如下几个配置

```java

    static final String PREFIX = "com.alipay.sofa.rpc";
    /* Bolt start*/
    private String      boltPort;
    private String      boltThreadPoolCoreSize;
    private String      boltThreadPoolMaxSize;
    private String      boltThreadPoolQueueSize;
    private String      boltAcceptsSize;
``` 

那么在 application.properties 文件中，如果要配置 rpc 的端口。则可以写

```xml
com.alipay.sofa.rpc.bolt.port=12201
```


如果要改变核心线程数，则可以写作
```xml
com.alipay.sofa.rpc.bolt.thread.pool.core.size=400
```
来改变端口，也可以按照 Spring Boot的规范，按照驼峰，下划线等进行书写。