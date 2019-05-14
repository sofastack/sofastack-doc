# 快速入门

本文档将演示了如何应用 SOFARPC 进行服务的发布和引用。
本例将在本地模拟服务端启动监听一个端口并发布一个服务，客户端引用该服务进行直连调用。

您可以直接在工程下找到本文档的[示例代码](https://github.com/sofastack/sofa-rpc/tree/master/example/src/test/java/com/alipay/sofa/rpc/quickstart)。

## 创建工程
需要安装 JDK 6 及以上 和 Maven 3 以上.

我们新建一个 Maven 工程，并引入 SOFARPC 的依赖。

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofa-rpc-all</artifactId>
    <version>最新版本</version>
</dependency>
```

注：最新版本可以从 https://github.com/sofastack/sofa-rpc/releases 里找到。


## 编写服务端实现
第一步：创建接口

```java
/**
 * Quick Start demo interface
 */
public interface HelloService {
    String sayHello(String string);
}
```

第二步：创建接口实现

```java
/**
 * Quick Start demo implement
 */
public class HelloServiceImpl implements HelloService {
    @Override
    public String sayHello(String string) {
        System.out.println("Server receive: " + string);
        return "hello " + string + " ！";
    }
}
```

第三步：编写服务端代码

```java
/**
 * Quick Start Server
 */
public class QuickStartServer {

    public static void main(String[] args) {
        ServerConfig serverConfig = new ServerConfig()
                .setProtocol("bolt") // 设置一个协议，默认bolt
                .setPort(12200) // 设置一个端口，默认12200
                .setDaemon(false); // 非守护线程

        ProviderConfig<HelloService> providerConfig = new ProviderConfig<HelloService>()
                .setInterfaceId(HelloService.class.getName()) // 指定接口
                .setRef(new HelloServiceImpl()) // 指定实现
                .setServer(serverConfig); // 指定服务端

        providerConfig.export(); // 发布服务
    }
}
```

## 编写客户端实现
第一步：拿到服务端接口

一般服务端会通过`jar`的形式将接口类提供给客户端。而在本例中，由于服务端和客户端在一个工程所以跳过。

第二步：编程客户端代码

```java
/**
 * Quick Start client
 */
public class QuickStartClient {
    public static void main(String[] args) {
        ConsumerConfig<HelloService> consumerConfig = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName()) // 指定接口
            .setProtocol("bolt") // 指定协议
            .setDirectUrl("bolt://127.0.0.1:12200"); // 指定直连地址
        // 生成代理类
        HelloService helloService = consumerConfig.refer();
        while (true) {
            System.out.println(helloService.sayHello("world"));
            try {
                Thread.sleep(2000);
            } catch (Exception e) {
            }
        }
    }
}
```

## 运行
分别启动服务端和客户端，观察运行效果。

服务端将打印：

> Server receive: world

客户端将打印：

> hello world ！


## 更多
更多示例请参考：[example](https://github.com/sofastack/sofa-rpc/tree/master/example)

SOFABoot 示例请参考：[sofaboot-sample-with-rpc](https://github.com/sofastack/sofa-boot/tree/master/sofaboot-samples/sofaboot-sample-with-rpc)
