## 调用方式
SOFARPC 提供了多种调用方式满足不同的场景。

### 同步
同步调用方式，客户端发起调用后会等待服务端返回结果再进行后续的操作。这是 SOFARPC 的默认调用方式，无需进行任何设置即可。

### 异步
客户端发起调用后不会等到服务端的结果，继续执行后面的业务逻辑。服务端返回的结果会被 SOFARPC 缓存，当客户端需要结果的时候，需要主动获取。
API 方式如下：
```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())
            .setInvokeType(RpcConstants.INVOKER_TYPE_FUTURE);
```
在 SOFABoot 中如下配置：
```xml
<sofa:reference id="sampleFacadeReferenceBolt" interface="com.alipay.sofa.rpc.bean.SampleFacade" >
    <sofa:binding.bolt>
        <sofa:global-attrs type="future"/>
    </sofa:binding.bolt>
</sofa:reference>
```
如上设置为异步调用的方式。
获取结果有两种方式：
* 直接获取结果。
第一个参数是获取结果的超时时间，第二个参数表示是否清除线程上下文中的结果。
```java
String result = (String)SofaResponseFuture.getResponse(0,true);
```
* 获取原生Futrue
该种方式会获取jdk原生的Future，参数表示是否清除线程上下文中的结果。
```java
Future future = SofaResponseFuture.getFuture(true);
```
在 SOFABoot 中如下配置：
```xml
<sofa:reference id="sampleFacadeReferenceBolt" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt>
        <sofa:global-attrs type="future"/>
    </sofa:binding.bolt>
</sofa:reference>
```

### 回调
客户端提前设置一个回调实现类，在发起调用后不会等待结果。 SOFA-RPC 在获取到服务端的接口后会自动执行该回调实现。
客户端回调类需要实现 `com.alipay.sofa.rpc.core.invoke.SofaResponseCallback` 接口
API 方式如下：
```java
SofaResponseCallback sofaResponseCallbackImpl = new SofaResponseCallbackImpl();

ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())
            .setInvokeType(RpcConstants.INVOKER_TYPE_CALLBACK)
            .setOnReturn(sofaResponseCallbackImpl)
```
这是服务级别的设置，也可以进行调用级别的设置。
```java
RpcInvokeContext.getContext().setResponseCallback(sofaResponseCallbackImpl);
```
在 SOFABoot 中如下配置：
```xml
<bean id="callback" class="xxx.xxx.sofaResponseCallbackImpl"/>
<sofa:reference id="sampleFacadeReferenceBolt" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt>
        <sofa:global-attrs type="callback" callback-ref="callback"/>
    </sofa:binding.bolt>
</sofa:reference>
```

### 单向
客户端发送请求后不会等待服务端返回的结果，并且会忽略服务端的处理结果。
API 方式如下：
```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()
            .setInterfaceId(HelloService.class.getName())
            .setInvokeType(RpcConstants.INVOKER_TYPE_ONEWAY);
```
在 SOFABoot 中如下配置：
```xml
<bean id="callback" class="xxx"/>
<sofa:reference id="sampleFacadeReferenceBolt" interface="com.alipay.sofa.rpc.bean.SampleFacade" local-first="false">
    <sofa:binding.bolt>
        <sofa:global-attrs type="oneway"/>
    </sofa:binding.bolt>
</sofa:reference>
```