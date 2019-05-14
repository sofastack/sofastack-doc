## Client fuse

SOFARPC is integrated Hystrix provides fuse capability and is currently available in the first preview version. More information about Hystrix can be found in [Hystrix Official Documentation] (https://github.com/Netflix/Hystrix), Hystrix integration capabilities are provided primarily by [ScienJus] (https://github.com/ScienJus), thanks for contribution.

Next, let's talk about how to experience the fuse capability of Hystrix. The following example uses the `SOFARPC 5.5.0` version. More `Hystrix` configuration and `SOFABoot` integration usage will be provided in subsequent releases, so stay tuned.

### Work preparation

1. The Hystrix module is not loaded directly as an optional module by default. If you need to use it, you need to actively add the Hystrix maven dependency:
```xml
<dependency>
        <groupId>com.netflix.hystrix</groupId>
        <artifactId>hystrix-core</artifactId>
        <version>1.5.12</version>
</dependency>
```
2. By explicitly opening `Hystrix` by configuration, `HystrixFilter` will be loaded automatically:
```java
// Open globally
RpcConfigs.putValue(HystrixConstants.SOFA_HYSTRIX_ENABLED, true);
// Open for a specific Consumer
ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
        .setInterfaceId(HelloService.class.getName())
        .setParameter(HystrixConstants.SOFA_HYSTRIX_ENABLED, String.valueOf(true));
```

### FallbackFactory

The `FallbackFactory` interface mainly provides the injection capability of the `Fallback` implementation, which is used to automatically perform the degraded logic when `Hystrix` executes an exception (throws an exception, timeout, thread pool rejection, and blown).

1. Define the interface `Fallback` implementation:
```java
public class HelloServiceFallback implements HelloService {
    @Override
    public String sayHello(String name, int age) {
        return "fallback " + name + " from server! age: " + age;
    }
}
```
2. Inject `Fallback` implementation:
```java
ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
        .setInterfaceId(HelloService.class.getName())
        .setParameter(HystrixConstants.SOFA_HYSTRIX_ENABLED, String.valueOf(true));
// You can directly inject Fallback implementation directly using the default FallbackFactory
SofaHystrixConfig.registerFallback(consumerConfig, new HelloServiceFallback());
// You can also customize FallbackFactory to directly inject FallbackFactory
SofaHystrixConfig.registerFallbackFactory(consumerConfig, new HelloServiceFallbackFactory());
```
3. When the server responds with a failure, the client automatically triggers the `Fallback` logic execution.

### SetterFactory

`SetterFactory` provides `Hystrix` fine-grained configuration capabilities. SOFARPC has provided the default `DefaultSetterFactory` to generate the `Setter` for each caller. If there is a more customized description, it can also be provided for each `ConsumerConfig`. Customize `SetterFactory`.
```java
SofaHystrixConfig.registerSetterFactory(consumerConfig, new CustomSetterFactory());
```

In the implementation provided by default, `GroupKey` is `InterfaceId`, and `CommandKey` is the name of the method.

### Support Hystrix version information

SOFARPC: [5.5.0](https://github.com/sofastack/sofa-rpc/releases), SOFABoot: [2.5.3](https://github.com/sofastack/sofa-boot/releases/)。

SOAF RPC Integration Verification Hystrix version: `1.5.12`.

