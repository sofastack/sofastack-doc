## 调用方式

SOFARPC 在 Bolt 协议下提供了多种调用方式满足不同的场景。

### 同步

在同步的调用方式下，客户端发起调用后会等待服务端返回结果再进行后续的操作。这是 SOFARPC 的默认调用方式，无需进行任何设置即可。

### 异步

异步调用的方式下，客户端发起调用后不会等到服务端的结果，继续执行后面的业务逻辑。服务端返回的结果会被 SOFARPC 缓存，当客户端需要结果的时候，再主动调用 API 获取。如果需要将一个服务设置为异步的调用方式，在对应的使用方式下设置 `type` 属性即可：

#### XML 方式

在 XML 方式下，设置 `<sofa:global-attrs>` 标签的 `type` 属性为 `future` 即可：

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs type="future"/>
    </sofa:binding.bolt>
</sofa:reference>
```

#### Annotation 方式

在 Annotation 方式下，设置 `@SofaReferenceBinding` 的 `invokeType` 属性为 `future` 即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", invokeType = "future"))
private SampleService sampleService;
```

#### Spring 环境下 API 方式

在 Spring 环境下使用 API，设置 `BoltBindingParam` 的 `type` 属性即可：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setType("future");
```

#### 在非 Spring 环境下 API 方式

在非 Spring 环境下使用 SOFARPC 裸 API，设置 `ConsumerConfig` 的 `invokeType` 属性即可：

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setInvokeType("future");
```

#### 获取调用结果

使用异步调用的方式，目前提供了两种方式来获取异步调用的结果：

##### 直接获取结果

用户可以通过以下的方式来直接获取异步调用的结果：

```java
String result = (String)SofaResponseFuture.getResponse(0, true);
```

其中第一个参数是获取结果的超时时间，第二个参数表示是否清除线程上下文中的结果。

##### 获取 JDK 原生 Future

用户可以通过以下的方式来获取 JDK 的原生的 Future 对象，再可以从任意地方去调用这个 Future 对象来获取结果：

```java
Future future = SofaResponseFuture.getFuture(true);
```

其中的第一个参数表示是否清除线程上下文中的结果。

### 回调

SOFARPC Bolt 协议的回调方式可以让 SOFARPC 在发起调用后不等待结果，在客户端收到服务端返回的结果后，自动回调用户实现的一个回调接口。

使用 SOFARPC Bolt 协议的回调方式，首先需要实现一个回调接口，并且在对应的配置中设置回调接口，再将调用方式设置为 `callback`。

#### 实现回调接口

SOFARPC 提供了一个回调的接口 `com.alipay.sofa.rpc.core.invoke.SofaResponseCallback`，用户使用 SOFARPC Bolt 协议的回调方式，首先需要实现这个接口，该接口提供了三个方法：

* `onAppResponse`：当客户端接收到服务端的正常返回的时候，SOFARPC 会回调这个方法。
* `onAppException`：当客户端接收到服务端的异常响应的时候，SOFARPC 会回调这个方法。
* `onSofaException`：当 SOFARPC 本身出现一些错误，比如路由错误的时候，SOFARPC 会回调这个方法。

#### 设置回调接口

实现回调接口之后，用户需要将实现类设置到对应的服务引用配置中，并且将调用方式设置为 callback。

SOFARPC 为设置回调接口提供了两种方式，分别为 Callback Class 和 Callback Ref。Callback Class 的方式直接设置回调的类名，SOFARPC 会通过调用回调类的默认构造函数的方式生成回调类的实例。Callback Ref 的方式则为用户直接提供回调类的实例。

##### XML 方式

如果通过 XML 的方式引用服务，将 `<sofa:global-attrs>` 标签的 `type` 属性设置为 `callback`，并且设置 `callback-ref` 或者 `callback-class` 属性即可：

```xml
<bean id="sampleCallback" class="com.example.demo.SampleCallback"/>
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs type="callback" callback-ref="sampleCallback"/>
    </sofa:binding.bolt>
</sofa:reference>
```

在 XML 的方式下，`callback-ref` 的值需要是回调类的 Bean 名称。

##### Annotation 方式

如果通过 Annotation 的方式引用服务，设置 `@SofaReferenceBinding` 注解的 `invokeType` 属性为 `callback`，并且设置 `callbackClass` 或者 `callbackRef` 属性即可：

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt",
            invokeType = "callback",
            callbackRef = "sampleCallback"))
private SampleService sampleService;
```

在 Annotation 的方式下，`callbackRef` 属性的值需要是回调类的 Bean 名称。

##### Spring 环境 API 方式

如果在 Spring 或者 Spring Boot 的环境下使用 API 的方式，设置 `BoltBindingParam` 的 `type` 属性为 `callback`，并且设置 `callbackClass` 或者 `callbackRef` 属性即可：

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setType("callback");
boltBindingParam.setCallbackClass("com.example.demo.SampleCallback");
```

##### 非 Spring 环境下 API 方式

如果在非 Spring 环境下使用 SOFARPC 的裸 API，调用 `setInvokeType` 将类型设置成 `callback`，并且调用 `setOnReturn` 设置回调类即可：

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setInvokeType("callback")
    .setOnReturn(new SampleCallback());
```

##### 在调用级别设置回调接口

除了在服务级别设置回调接口之外，还可以在调用级别设置回调接口，方式如下：

```java
RpcInvokeContext.getContext().setResponseCallback(new SampleCallback());
```

### 单向

当客户端发送请求后不关心服务端返回的结果时，可以使用单向的调用方式，这种方式会在发起调用后立即返回 null，并且忽略服务端的返回结果。

使用单向的方式只需要将调用方式设置为 `oneway` 即可，设置方式和将调用方式设置为 `future` 或者 `callback` 一样，这里不再重复讲述，可以参考上面的文档中提供的设置方式。

需要特别注意的是，由于单向的调用方式会立即返回，所以所有的超时设置在单向的情况下都是无效的。