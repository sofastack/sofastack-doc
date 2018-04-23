# 基本特性

其中关于 SOFABoot 环境下的使用方式具体可见 [sofa-boot-starter 用户手册](https://github.com/alipay/sofa-rpc-boot-projects/wiki/UserGuide)
## 服务发布/服务引用
服务发布与引用如前文编程界面所示。其中一些特性如下：

* 同一服务发布多种协议
如下构建多个 ServerConfig 设置给 ProviderConfig 。在 SOFABoot 环境中配置多个 binding 即可。
```java
 List<ServerConfig> serverConfigs = new ArrayList<ServerConfig>();
 serverConfigs.add(serverConfigA);
 serverConfigs.add(serverConfigB);
 
 providerConfig.setServer(serverConfigs);
```
```xml
<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt/>
    <sofa:binding.rest/>
    <sofa:binding.dubbo/>
</sofa:service>
```

* 同一服务注册多个注册中心。
如下构建多个多个 RegistryConfig 设置给 ProviderConfig 。
```java
List<RegistryConfig> registryConfigs = new ArrayList<RegistryConfig>();
registryConfigs.add(registryA);
registryConfigs.add(registryB);

providerConfig.setRegistry(registryConfigs);
```

* 方法级参数设置。
API 方式中，使用相应的对象 set 方法即可为其设置参数。特别说明的是 SOFARPC 提供了 MethodConfig 进行方法级别的参数设置，如下：
```java
MethodConfig methodConfigA = new MethodConfig();
MethodConfig methodConfigB = new MethodConfig();

List<MethodConfig> methodConfigs = new ArrayList<MethodConfig>();
methodConfigs.add(methodConfigA);
methodConfigs.add(methodConfigB);
        
providerConfig.setMethods(methodConfigs);  //服务端设置
consumerConfig.setMethods(methodConfigs);  //客户端设置
```
* 在 SOFABoot 环境中设置参数：
```xml
<sofa:reference id="personReferenceBolt" interface="com.alipay.sofa.boot.examples.demo.rpc.bean.PersonService">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000" address-wait-time="2000"/>  <!-- 调用超时；地址等待时间。 -->
        <sofa:route target-url="127.0.0.1:22000"/>  <!-- 直连地址 -->
        <sofa:method name="sayName" timeout="3000"/> <!-- 方法级别配置 -->
    </sofa:binding.bolt>
</sofa:reference>

<sofa:service ref="sampleFacadeImpl" interface="com.alipay.sofa.rpc.bean.SampleFacade">
    <sofa:binding.bolt>
        <sofa:global-attrs timeout="3000"/>
        <sofa:method name="sayName" timeout="2000"/>
    </sofa:binding.bolt>
</sofa:service>
```

## 直连调用
SOFARPC 支持指定地址进行调用的场景。使用方式如下，设置直连地址即可。
```java
ConsumerConfig<HelloService> consumer = new ConsumerConfig<HelloService>()        
            .setInterfaceId(HelloService.class.getName())        
            .setRegistry(registryConfig)        
            .setDirectUrl("bolt://127.0.0.1:12201");
```

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

### 泛化
客户端在不需要依赖服务端的接口情况下就能够发起调用。
```java
ConsumerConfig<GenericService> consumerConfig = new ConsumerConfig<GenericService>()
           .setInterfaceId("com.alipay.sofa.rpc.quickstart.HelloService")
           .setGeneric(true);
GenericService testService = consumerConfig.refer();

String result = (String) testService.$invoke("sayHello", new String[] { "java.lang.String" },new Object[] { "1111" });
```
如上通过 setGeneric 设置该服务为泛化服务，设置服务方的接口名。以 GenericService 作为泛化服务，通过 GenericService 就能够发起泛化调用了。发起调用时，需要传入方法名，方法类型，方法参数。

如果参数或者返回结果在客户端也需要泛化表示。可以通过 GenericObject 来实现。
```java
GenericObject param = new GenericObject("com.alipay.sofa.rpc.invoke.generic.TestObj");
              
genericObject.putField("str", "xxxx");
genericObject.putField("num", 222);

GenericObject result = (GenericObject) testService.$genericInvoke("echoObj",
                    new String[] { "com.alipay.sofa.rpc.invoke.generic.TestObj" },
                    new Object[] { param });

String str = result.getField("str");
String num = result.getField("num");
```
如上就能获得序列化结果。完整的泛化调用方式使用说明如下：
```java
/**
* Java Bean
*/
public class People {
    private String name;
    private int    age;
    
    // getters and setters
}


/**
 * 服务方提供的接口
 */
interface SampleService {
   String hello(String arg);
   People hello(People people);
   String[] hello(String[] args);
}

/**
 * 客户端
 */
public class ConsumerClass {
   GenericService genericService;

   public void do() {
      // 1. $invoke仅支持方法参数类型在当前应用的 ClassLoader 中存在的情况
      genericService.$invoke("hello", new String[]{ String.class.getName() }, "I'm an arg");
      
      // 2. $genericInvoke支持方法参数类型在当前应用的 ClassLoader 中不存在的情况。
      // 2.1 构造参数
      GenericObject genericObject = new GenericObject("com.alipay.sofa.rpc.test.generic.bean.People"); // 构造函数中指定全路径类名
      genericObject.putField("name", "Lilei"); // 调用putField，指定field值
      genericObject.putField("age", 15);
      
      // 2.2 进行调用，不指定返回类型，返回结果类型为GenericObject
      Object obj = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject });
      Assert.assertTrue(obj.getClass == GenericObject.class);
      
      // 2.3 进行调用，指定返回类型
      People people = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject }, People.class);
	  
      // 2.4 进行调用，参数类型是数组类型
      String[] result = (String[]) proxy.$genericInvoke("hello", new String[]{new String[0].getClass().getName()}, new Object[]{ new String[]{"args"} });
```


## 负载均衡
SOFARPC 提供多种负载均衡算法，目前支持以下五种：

| 类型              | 名称                 | 描述                                            |
|:-----------------|:--------------------|:-----------------------------------------------|
| random           | 随机算法             | 默认负载均衡算法。                                |
| localPref        | 本地优先算法          | 优先发现是否本机发布了该服务，如果没有再采用随机算法。 |
| roundRobin       | 轮询算法             | 方法级别的轮询，各个方法间各自轮询，互不影响。        |
| consistentHash   | 一致性hash算法       | 同样的方法级别的请求会路由到同样的节点。             |
| weightRoundRobin | 按权重负载均衡轮询算法 | 按照权重对节点进行轮询。性能较差，不推荐使用。        |

通过 ConsumerConfig 即可设置。
```java
ConsumerConfig consumerConfig = new ConsumerConfig();
consumerConfig.setLoadbalance("random");
```
## 自定义 Filter
SOFA-RPC 提供了一套良好的可扩展性机制，为各个模块提供 SPI 的能力。 SOFA-RPC 对请求与响应的过滤链处理方式是通过多个过滤器 Filter 来进行具体的拦截处理，该部分可由用户自定义 Filter 扩展，自定义 Filter 的执行顺序在内置 Filter 之后。具体方式如下：

1. 新建自定义 Filter 。
```java
public class CustomFilter extends Filter {    
    @Override    
    public boolean needToLoad(FilterInvoker invoker) {        
        return true;    
    }    
    @Override    
    public SofaResponse invoke(FilterInvoker invoker, SofaRequest request) throws SofaRpcException {        
        SofaResponse response = invoker.invoke(request);        
        return response;    
    }
}
```
2. 生效该自定义 Filter 到拦截器链中。这一步具体方式有三种。
  方式1：API方式。该种方式能够生效到指定的 provider 或 consumer 。
```java
// 服务提供者
providerConfig.setFilterRef(Arrays.asList(new CustomFilter()));
// 服务调用者
consumerConfig.setFilterRef(Arrays.asList(new CustomFilter()));
```
  方式2：在类上加上 @Extension 注解+配置扩展文件方式。
```java
@Extension("customer")
public class CustomFilter extends Filter {    
    @Override    
    public boolean needToLoad(FilterInvoker invoker) {        
        return true;    
    }    
    @Override    
    public SofaResponse invoke(FilterInvoker invoker, SofaRequest request) throws SofaRpcException {        
        SofaResponse response = invoker.invoke(request);        
        return response;    
    }
}
```
新建扩展文件 META-INF/services/sofa-rpc/com.alipay.sofa.rpc.filter.Filter 。内容如下：
```
customer=com.alipay.sofa.rpc.custom.CustomFilter
```
编码注入。
```java
// 服务提供者
providerConfig.setFilter(Arrays.asList("customer"));
// 服务调用者
consumerConfig.setFilter(Arrays.asList("customer"));
```
方式三：在类上加上 @Extension 注解+ @AutoActive 注解方式+配扩展文件方式。该种方式利用 @AutoActive 注解代替了上述第二中方式的编码注入步骤，能够生效于所有 provider 或 consumer 。其中 providerSide 参数表示是否生效于服务端， consumerSide 参数表示是否生效于服务端。
```java
@Extension("customer")
@AutoActive(providerSide = true, consumerSide = true)
public class customerFilter extends Filter {
		// ...
}
```
## 自定义 Router
SOFA-RPC 中对服务地址的选择也抽象为了一条处理链，由每一个 Router 进行处理。同 Filter 一样， SOFA-RPC 对 Router 提供了同样的扩展能力。
```java
@Extension(value = "customerRouter")
@AutoActive(consumerSide = true)
public class CustomerRouter extends Router {
   
    @Override
    public void init(ConsumerBootstrap consumerBootstrap) {
        
    }
    @Override
    public boolean needToLoad(ConsumerBootstrap consumerBootstrap) {
       return ture;
    }
    @Override
    public List<ProviderInfo> route(SofaRequest request, List<ProviderInfo> providerInfos) {
        return providerInfos;
    }
```
新建扩展文件 META-INF/services/sofa-rpc/com.alipay.sofa.rpc.client.Router 。内容如下：
```
customerRouter=com.alipay.sofa.rpc.custom.CustomRouter
```
如上自定义了一个 CustomerRouter ，生效于所有消费者。其中 init 参数 ConsumerBootstrap 是引用服务的包装类，能够拿到 ConsumerConfig ，代理类，服务地址池等对象。 needToLoad 表示是否生效该 Router ， route 方法即筛选地址的方法。
