# ExtensionLoader

为了对 SOFARPC 各个环节的都有充足的可扩展性，SOFA-RPC定义了一套十分灵活的扩展机制，所有扩展实现都是**平等**的。

这套机制不管是对SOFA-RPC本身的开发者其使用者而言都是非常有用的。SOFA-RPC将其自身抽象为了多个模块，各个模块之间无显示依赖，通过SPI的方式进行交互。

这套扩展机制抽象了这一SPI的交互方式。如果你读了上面文档讲到的 Filter 和 Router，应该已经有所体会。

这里讲一下如何使方式进行扩展的。

SOFARPC 提供了 ExtensionLoader 的能力。


## 扩展点设计

SOFARPC 定义了一个注解 `@Extensible`，该注解标识在接口或者抽象类上，标识该类是一个扩展点。即告诉 SOFARPC 该类是可扩展的，需要寻找该扩展点的实现，同时也定义了寻找实现类的文件名称，是否单例。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE })
public @interface Extensible {

    /**
     * 指定自定义扩展文件名称，默认就是全类名
     *
     * @return 自定义扩展文件名称
     */
    String file() default "";

    /**
     * 扩展类是否使用单例，默认使用
     *
     * @return 是否使用单例
     */
    boolean singleton() default true;

    /**
     * 扩展类是否需要编码，默认不需要
     *
     * @return 是否需要编码
     */
    boolean coded() default false;
}
```

SOFARPC 同时定义了 `@Extension` 注解，标识该类是一个扩展实现类。也定义了扩展点在文件中寻找扩展实现时使用的名字。

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE })
public @interface Extension {
    /**
     * 扩展点名字
     *
     * @return 扩展点名字
     */
    String value();

    /**
     * 扩展点编码，默认不需要，当接口需要编码的时候需要
     *
     * @return 扩展点编码
     * @see Extensible#coded()
     */
    byte code() default -1;

    /**
     * 优先级排序，默认不需要，大的优先级高
     *
     * @return 排序
     */
    int order() default 0;

    /**
     * 是否覆盖其它低{@link #order()}的同名扩展
     *
     * @return 是否覆盖其它低排序的同名扩展
     * @since 5.2.0
     */
    boolean override() default false;

    /**
     * 排斥其它扩展，可以排斥掉其它低{@link #order()}的扩展
     *
     * @return 排斥其它扩展
     * @since 5.2.0
     */
    String[] rejection() default {};
}
```

## 新增扩展点

1.定义扩展点。

```java
@Extensible
public interface Person {

    void getName();
}
```

2.定义扩展实现

```java
@Extension("A")
public class PersonA implements Person{
    @Override
    public void getName() {
        System.out.println("li wei");
    }
}
```

3.编写扩展描述文件：`META-INF/services/sofa-rpc/com.alipay.sofa.rpc.extension.Person`。文件内容如下：

```plain
A=com.alipay.sofa.rpc.extension.PersonA
```

4.加载扩展点，获取到扩展实现类使用。

```java
Person person = ExtensionLoaderFactory.getExtensionLoader(Person.class).getExtension("A");
```

## 已有扩展点

如果想对 SOFARPC 的各个内置扩展点进行功能扩展，可直接实现已有扩展，配置扩展模式文件即可。

目前已有的扩展点如下：

接口名|中文名|备注|内置实现
:---------------------------------|:-------|:-----------------------|:-----
com.alipay.sofa.rpc.client.Client              |客户端    | |Failover、Failfast
com.alipay.sofa.rpc.client.ConnectionHolder    |连接管理器 | |AllConnect（全部连接）
com.alipay.sofa.rpc.client.AddressHolder       |地址管理器 | |单组、多组
com.alipay.sofa.rpc.client.LoadBalancer        |负载均衡   | |随机、轮询、最少并发、一致性hash、本机优先
com.alipay.sofa.rpc.client.Router              |路由器    | |
com.alipay.sofa.rpc.codec.Compressor           |压缩      | |snappy、~~quicklz~~
com.alipay.sofa.rpc.codec.Serializer           |序列化器  | |java、hessian、pb
com.alipay.sofa.rpc.filter.Filter              |拦截器    | |
com.alipay.sofa.rpc.protocol.Protocol          |协议      | |bolt、dubbo、rest
com.alipay.sofa.rpc.protocol.ProtocolDecoder   |协议解码   | |bolt
com.alipay.sofa.rpc.protocol.ProtocolEncoder   |协议编码   | |bolt
com.alipay.sofa.rpc.protocol.TelnetHandler     |telnet的响应| |version、help、ls
com.alipay.sofa.rpc.proxy.Proxy                |代理类    | |java、javassist
com.alipay.sofa.rpc.registry.Registry          |注册中心    | |zookeeper
com.alipay.sofa.rpc.server.Server              |服务端实现 | | bolt、rest
com.alipay.sofa.rpc.transport.ClientTransport  |客户端长连接实现| |netty
com.alipay.sofa.rpc.transport.ServerTransport  |服务端长连接实现| |netty
