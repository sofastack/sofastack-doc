# 《开发者手册》
# 如何编译
> 安装 JDK7 及以上，Maven 3.2.5 及以上。

直接下面代码，然后执行如下命令：
```
cd sofa-rpc
mvn clean install
```

# 架构图

SOFARPC 从下到上分为两层：

1. 核心层：包含了我们的 RPC 的核心组件（例如我们的各种接口、API、公共包）以及一些通用的实现（例如随机等负载均衡算法）。
2. 功能实现层：所有的功能实现层的用户都是平等的，都是基于扩展机制实现的。

![[架构图]()](./resources/dg_1.png)

蚂蚁内部使用的版本也只是开源版本上增加一些内部扩展而已。

当然你也可以增加自己三方扩展，参见：[扩展机制](#extensionloader)

## 模块划分

各个模块的实现类都只在自己模块中出现，一般不交叉依赖。需要交叉依赖的全部已经抽象到core或者common模块中。

目前模块划分如下:

![[模块划分]()](./resources/dg_2.png)

主要模块及其依赖如下：

模块名|子模块名|中文名|说明|依赖|
-------:|-------:|:-------|:------------------------|:-----
all     |        | 发布打包模块  |                     | 需要打包的全部模块
bom     |        |依赖管控模块  | 依赖版本管控                     | 无
example |        | 示例模块     |    | all
test    |        | 测试模块     | 包含集成测试 | all
core    |   api  |API模块       |各种基本流程接口、消息、上下文、扩展接口等  | common
core    |common  |公共模块      |utils、数据结构                   | exception
core    |exception|异常模块   |各种异常接口接口等 | common
bootstrap|       |启动实现模块   |启动类，发布或者引用服务逻辑、以及registry的操作 | core
proxy    |       |代理实现模块   |接口实现代理生成| core
client   |       |客户端实现模块 |发送请求、接收响应、连接维护、路由、负载均衡、同步异步等| core
server   |       |服务端实现模块 |启动监听、接收请求，发送响应、业务线程分发等| core
filter   |       |拦截器实现模块 |服务端和客户端的各种拦截器实现| core
codec    |       |编解码实现模块 |例如压缩，序列化等| core
protocol |       |协议实现模块   |协议的包装处理、协商| core
transport|       |网络传输实现模块 |TCP连接的建立，数据分包粘包处理，请求响应对象分发等| core
registry |       |注册中心实现模块|实现注册中心，例如zk等| core


## 公用模型

### 消息
内部全部使用 `SofaRequest` 和 `SofaResponse` 进行传递。

如果需要转换为其它协议，那么在真正调用和收到请求的时候，转换为实际要传输的对象。

可以对 `SofaRequest` 和 `SofaResponse` 进行写操作的模块：
- Invoker
- Filter
- ServerHandler
- Serialization 

对消息体是只读的模块：
- Cluster
- Router
- LoadBalance

### 日志

日志的初始化也是基于扩展机制。虽然是扩展，但是由于日志的加载应该是最早的，所以在 `rpc-config.json` 里有一个单独的 Key。

```json
{
  // 日志实现，日志早于配置加载，所以不能适应Extension机制
  "logger.impl": "com.alipay.sofa.rpc.log.MiddlewareLoggerImpl"
}
```

### 配置项
#### 使用者的 RPC 配置
用户的配置，例如端口配置（虽然已经开放对象中设置端口的字段，但是sofa默认是从配置文件里取的），线程池大小配置等。
- 通过 `SofaConfigs` 加载配置，调用 ExternalConfigLoader 读取外部属性。 
- 通过 `SofaConfigs` 提供的 API 进行获取。
- 所有内部配置的Key都在 `SofaOptions` 类
- 优先级： `System.property` > `sofa-config.properties`(每个应用一个) > `rpc-config.properties`

#### RPC 框架配置
框架自身的配置，例如默认序列化，默认超时等。 未来要一个ClassLoader一个。
- 通过 `RpcConfigs` 加载配置文件。 
- 通过 `RpcConfigs` 其提供的API进行获取和监听数据变化
- 所有内部配置的Key都在 `RpcOptions` 类
- 优先级： `System.property` > `custom rpc-config.json`（可能存在多个自定义，会排序） > `rpc-config-default.json`

### 常量
- 全局的基本常量在 `RpcConstants` 中。例如：
    - 调用方式 sync oneway 
    - 协议 bolt/grpc、 序列化 hessian/java/protobuf   
    - 上下文的key
    - 等等。
- 如果扩展实现自身的常量，请自行维护。
	- 例如 BOLT 协议的常量。
		- SERIALIZE_CODE_HESSIAN = 1
	    - PROTOCOL_TR = 13
 	- 例如 DSR 配置中心相关的常量。
		- _WEIGHT、_CONNECTTIMEOUT 这种 配置中心特有的key

### 地址
- 地址信息放到 `ProviderInfo` 类中
- `ProviderInfo` 的值主要分为三部分
	- 字段，一般是一些必须项目。 例如IP，端口，状态等；
	- 静态字段：例如应用名；
	- 动态字段：例如预热权重等；
- 字段枚举维护在 `ProviderInfoAttrs` 类中。

# 实现细节
## ExtensionLoader
为了对 SOFARPC 各个环节的都有充足的可扩展性，SOFA-RPC定义了一套十分灵活的扩展机制，所有扩展实现都是**平等**的。

这套机制不管是对SOFA-RPC本身的开发者其使用者而言都是非常有用的。SOFA-RPC将其自身抽象为了多个模块，各个模块之间无显示依赖，通过SPI的方式进行交互。

这套扩展机制抽象了这一SPI的交互方式。如果你读了上面文档讲到的 Filter 和 Router，应该已经有所体会。

这里讲一下如何使方式进行扩展的。

SOFARPC 提供了 ExtensionLoader 的能力。


### 扩展点设计

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

### 新增扩展点

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

### 已有扩展点

如果想对 SOFARPC 的各个内置扩展点进行功能扩展，可直接实现已有扩展，配置扩展模式文件即可。

目前已有的扩展点如下：

接口名|中文名|备注|内置实现
:---------------------------------|:-------|:-----------------------|:-----
com.alipay.sofa.rpc.client.Client              |客户端    | |Failover、Failfast
com.alipay.sofa.rpc.client.ConnectionHolder    |连接管理器 | |AllConnect（全部连接）
com.alipay.sofa.rpc.client.AddressHolder       |地址管理器 | |单组、多组
com.alipay.sofa.rpc.client.LoadBalancer        |负载均衡   | |随机、轮询、最少并发、一致性hash、本机优先
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

## Client 模块组件
客户端模块是一个较复杂的模块，这里包含了集群管理、路由、地址管理器、连接管理器、负载均衡器，还与代理、注册中心等模块交互。

参见：

![[client组件]()](./resources/dg_3.png)


# 版本发布
采用三位版本号，分别是主版本号、次版本号、修订版本号。例如 5.1.2。

参见: [http://semver.org/lang/zh-CN/](http://semver.org/lang/zh-CN/)。

- 主版本号：主版本号内的所有版本必须相互兼容；与其它主版本号不一定完全兼容，尽量向下兼容。
- 次版本号：代表新特性增强。版本号越大特性越丰富。
- 修订版本号：代表BugFix版本。只做bug修复使用，版本号越大越稳定。

最多同时维护两个版本，例如当前主干为 `5.3.0`，那么将会维护 `5.2.x` 的 bugfix 分支，而 `5.1.x` 遇到 bug 将不再修复，建议升级。

# 测试
## 单元测试
单元测试例子放到自己开发的模块下。

如果依赖了第三方服务端（例如Zookeeper），请手动加入 profile。参考 `registry-zookeeper` 模块代码。

如果依赖了其它模块要集成测试，请放到 `test/test-intergrated` 模块中。

如果还依赖了第三方服务端（例如Zookeeper），请放到 `test-intergrated-3rd` 模块中。

## 性能测试
关闭了一下默认开启项目：  
`-Dcontext.attachment.enable=false -Dserialize.blacklist.enable=false -Ddefault.tracer= -Dlogger.impl=com.alipay.sofa.rpc.log.SLF4JLoggerImpl -Dmultiple.classloader.enable=false -Devent.bus.enable=false`

我们对 BOLT+hessian 进行了压测。

服务端：4C8G 虚拟机，千M网络，jdk1.8.0_111；

客户端：50个客户端并发请求。

| 协议 | 请求 | 响应 | 服务端 | TPS | 平均RT(ms) | 
| :--- | :--- | :--- | :--- | :--- | :--- | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 10000 | 1.93 |
| bolt+hessian | 1K String | 1K String | 直接返回 | 20000 | 4.13 | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 30000 | 7.32 |
| bolt+hessian | 1K String | 1K String | 直接返回 | 40000 | 15.78 | 
| bolt+hessian | 1K String | 1K String | 直接返回 | 50000(接近极限,错误率0.3%） | 26.51 |
