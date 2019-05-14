## maven coordinator
```
<dependency>
  <groupId>com.alipay.sofa</groupId>
  <artifactId>bolt</artifactId>
  <version>${version}</version>
</dependency>
```
> check [release note](https://github.com/sofastack/sofa-bolt/releases) for version

# 1. 基础功能
## 1.1 实现用户请求处理器 (UserProcessor)
我们提供了两种用户请求处理器，SyncUserProcessor 与 AsyncUserProcessor。
二者的区别在于，前者需要在当前处理线程以return返回值的形式返回处理结果；而后者，有一个 AsyncContext 存根，可以在当前线程，也可以在异步线程，调用 `sendResponse` 方法返回处理结果。示例可参考如下两个类：
   * [同步请求处理器](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/common/SimpleServerUserProcessor.java)
   * [异步请求处理器](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/common/AsyncServerUserProcessor.java)
## 1.2 实现连接事件处理器 (ConnectionEventProcessor)
我们提供了两种事件监听，建连事件（ConnectionEventType.CONNECT）与断连事件（ConnectionEventType.CLOSE），用户可以创建自己的事件处理器，并注册到客户端或者服务端。客户端与服务端，都可以监听到各自的建连与断连事件。
  * [处理连接建立事件](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/common/CONNECTEventProcessor.java)
  * [处理连接断开事件](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/common/DISCONNECTEventProcessor.java)
## 1.3 客户端与服务端初始化 (RpcClient，RpcServer)
我们提供了一个 RpcClient 与 RpcServer，经过简单的必要功能初始化，或者功能开关，即可使用。一个最简单的例子如下：
  * [客户端初始化示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/RpcClientDemoByMain.java)
  * [服务端初始化示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/RpcServerDemoByMain.java)
## 1.4 基础通信模型
我们提供了四种通信模型：
   * Oneway 调用
      * 当前线程发起调用后，不关心调用结果，不做超时控制，只要请求已经发出，就完成本次调用。注意 Oneway 调用不保证成功，而且发起方无法知道调用结果。因此通常用于可以重试，或者定时通知类的场景，调用过程是有可能因为网络问题，机器故障等原因，导致请求失败。业务场景需要能接受这样的异常场景，才可以使用。
      * [示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L101)
   * Sync 同步调用
      * 当前线程发起调用后，需要在指定的超时时间内，等到响应结果，才能完成本次调用。如果超时时间内没有得到结果，那么会抛出超时异常。这种调用模式最常用。注意要根据对端的处理能力，合理设置超时时间。
      * [示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L120)
   * Future调用
      * 当前线程发起调用，得到一个 RpcResponseFuture 对象，当前线程可以继续执行下一次调用。可以在任意时刻，使用 RpcResponseFuture 对象的 get() 方法来获取结果，如果响应已经回来，此时就马上得到结果；如果响应没有回来，则会阻塞住当前线程，直到响应回来，或者超时时间到。
      * [示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L144)
   * Callback异步调用
      * 当前线程发起调用，则本次调用马上结束，可以马上执行下一次调用。发起调用时需要注册一个回调，该回调需要分配一个异步线程池。待响应回来后，会在回调的异步线程池，来执行回调逻辑。
      * [示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L168)
## 1.5 日志打印
SOFABolt 只依赖 SLF4J 作为日志门面。同时提供了 log4j、log4j2、logback 三种日志模板，使用者只需要在运行时依赖某一种日志实现，我们依赖的 sofa-common-tools 组件，会在运行时动态感知是哪一种日志实现，同时加载正确的日志模板，进行打印。日志会打印在 `~/logs/bolt/` 目录下面，包括如下几种日志：
   * common-default.log：默认日志，打印一些客户端、服务器启动、关闭等通信过程的普通日志
   * common-error.log：异常日志，框架运行过程中出现的异常
   * connection-event.log：连接事件日志
   * remoting-rpc.log：RPC 协议相关的日志
关于日志依赖，可以参考[日志实现依赖参考](log_implementation_jar)

# 2. 进阶功能
## 2.1 请求上下文
在调用过程中，我们还提供了带 InvokeContext 的接口，并一路传递下去，可以在自定义序列化器，用户请求处理器中获得。我们分为两种场景来使用请求上下文：
   * 客户端：用户可以设置一些针对本次请求生效的参数，比如序列化类型，是否开启crc等机制。同时可以从上下文中获取建连耗时，连接信息等。
   * 服务端：用户可以从用户请求处理器中获得请求到达后的排队耗时，连接信息等
   * 注意：客户端与服务端的上线文是独立的，即客户端设置的上下文只在客户端可见，对服务端不可见；反之同理。
   * [使用示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/invokecontext/BasicUsage_InvokeContext_Test.java#L128)

## 2.2 双工通信
除了服务端可以注册用户请求处理器，我们的客户端也可以注册用户请求处理器。此时，服务端就可以发起对客户端的调用，也可以使用 [1.4](https://github.com/sofastack/sofa-bolt/wiki/SOFA-Bolt-Handbook#14-%E5%9F%BA%E7%A1%80%E9%80%9A%E4%BF%A1%E6%A8%A1%E5%9E%8B) 提到了任何一种通信模型。
   * [示例1：使用 Connection 对象的双工通信](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L223)，注意使用 Connection 对象的双工通信，服务端需要通过事件监听处理器或者用户请求处理器，自己保存好 Connection 对象。
   * [示例2：使用 Address 的双工通信](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/demo/BasicUsageDemoByJunit.java#L263)，注意使用 Address 方式的双工通信，需要在初始化 RpcServer 时，打开 manageConnection 开关，表示服务端会根据客户端发起的建连，维护一份地址与连接的映射关系。默认不需要双工通信的时候，这个功能是关闭的。
## 2.3 建立多连接与连接预热
通常来说，点对点的直连通信，客户端和服务端，一个 IP 一个连接对象就够用了。不管是吞吐能力还是并发度，都能满足一般业务的通信需求。而有一些场景，比如不是点对点直连通信，而是经过了 LVS VIP，或者 F5 设备的连接，此时，为了负载均衡和容错，会针对一个 URL 地址建立多个连接。我们提供如下方式来建立多连接，即发起调用时传入的 URL 增加如下参数 `127.0.0.1:12200?_CONNECTIONNUM=30&_CONNECTIONWARMUP=true`，表示针对这个 IP 地址，需要建立30个连接，同时需要预热连接。其中预热与不预热的区别是：
   * 预热：即第一次调用（比如 Sync 同步调用），就建立30个连接
   * 不预热：每一次调用，创建一个连接，直到创建满30个连接
   * [使用示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/addressargs/AddressArgs_CONNECTIONNUM_Test.java#L234)
## 2.4 自动断连与重连
通常 RPC 调用过程，是不需要断链与重连的。因为每次 RPC 调用过程，都会校验是否有可用连接，如果没有则新建一个。但有一些场景，是需要断链和保持长连接的：
  * 自动断连：比如通过 LVS VIP 或者 F5 建立多个连接的场景，因为网络设备的负载均衡机制，有可能某一些连接固定映射到了某几台后端的 RS 上面，此时需要自动断连，然后重连，靠建连过程的随机性来实现最终负载均衡。注意，开启了自动断连的场景，通常需要配合重连使用。
  * 重连：比如客户端发起建连后，由服务端来通过双工通信，发起请求到客户端。此时如果没有重连机制，则无法实现。
  * [使用示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/connectionmanage/ScheduledDisconnectStrategyTest.java#L246)，注意考虑一个进程可能会有多个 SOFABolt 的通信实例，我们提供了全局开关以及用户开关两种开关方式：
  
  ```
  // 通过系统属性来开和关，如果一个进程有多个 RpcClient，则同时生效
  System.setProperty(Configs.CONN_MONITOR_SWITCH, "true");
  System.setProperty(Configs.CONN_RECONNECT_SWITCH, "true");

  // 通过用户开关来开和关，只对当前 RpcClient实例起作用
  client.enableReconnectSwitch();
  client.enableConnectionMonitorSwitch();
  ```

## 2.5 序列化与反序列化器
默认序列化和反序列化目前我们推荐使用的是 Hessian，但考虑到不同的场景需求，我们支持默认序列化器的扩展，以及自定义序列化器的功能特性。
   * 扩展序列化器：实现一个继承 Serializer 的序列化器，然后通过 SerializerManager 注册，指定一个 index。使用示例如下：
   
   ```
   // 1. 实现 Serializer
   public class HessianSerializer implements Serializer {
        @Override
        public byte[] serialize(Object obj) throws CodecException {
            ...
        }

        @Override
        public <T> T deserialize(byte[] data, String classOfT) throws CodecException {
        }
   }
   // 2. 注册
   public static final byte    Hessian2    = 1;
   SerializerManager.addSerializer(Hessian2, new HessianSerializer());

   // 3. 通过系统属性来设置生效
    System.setProperty(Configs.SERIALIZER, String.valueOf(Hessian2));
   ```
   
   * 自定义序列化器：实现一个 CustomSerializer 类，可以针对 Header，Content 做自定义的序列化和反序列化。同时我们在接口上提供了 InvokeContext，因此序列化和反序列化的逻辑，可以根据请求上下文来做动态的调整
      * [使用示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/serializer/CustomSerializerCodecTest.java#L133)

# 3. 高级功能
## 3.1 开启IO线程处理机制
默认情况下，我们使用最佳实践的线程模型来处理请求，即尽可能少的占用 IO 线程，但有一些场景，比如计算过程非常简单，希望减少线程切换，尽可能大的增加 IO 吞吐量的场景。此时我们提供了一个开关，来让业务处理也在 IO 线程执行。
   * [使用示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/userprocessor/processinio/SpecificServerUserProcessor.java#L126)
## 3.2 启用用户处理器多线程池机制
请求处理过程，默认是一个线程池，那么当这个线程池出现问题后，则会造成整体的吞吐量降低。而有些业务场景，希望对他们核心的请求处理过程，单独分配一个线程池。避免不同请求互相影响。此时我们提供了一个线程池选择器：
   * 实现一个线程池选择器，[示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/userprocessor/executorselector/DefaultExecutorSelector.java)
   * 然后设置到用户请求处理器里面，调用过程即可根据选择器的逻辑来选择对应的线程池，[示例](https://github.com/sofastack/sofa-bolt/blob/master/src/test/java/com/alipay/remoting/rpc/userprocessor/executorselector/BasicUsage_ExecutorSelector_Test.java#L91)
## 3.3 请求处理超时 FailFast 机制
当服务端接收到请求后，如果线程池队列的排队等待时间已经超过了客户端发起调用时设置的超时时间，那么本次调用可以直接丢弃，因为请求，对于客户端来说已经无用了（注意：oneway调用方式该机制不起作用，因为不用设置超时时间）。默认情况下，我们这个功能都是开启的；考虑到有用户可能会需要自己来做是否丢弃请求的判断，同时打印一些日志来自己做记录，我们提供了一个开关来控制这个功能：
   * 开关控制
   
   ```
    @Override
    public boolean timeoutDiscard() {
        return false;// true表示开启自动丢弃，false表示关闭自动丢弃，用户在之后的处理processor里，可自行决策
    }
   ```

   * 判断超时与打印日志
   
   ```
    public class SimpleClientUserProcessor extends SyncUserProcessor<RequestBody> {
       @Override
       public Object handleRequest(BizContext bizCtx, RequestBody request) throws Exception {
           if(bizCtx.isRequestTimeout()){
              log.info("arrive time: {}", bizCtx.getArriveTimestamp());
              ...
           }
       }
    }
   ```

## 3.3 定制协议
对于通信类型比较简单的场景，我们直接复用 RPC 通信协议，使用对应的通信类型，就能解决大部分问题。而有一些场景，比如消息中间件，数据库中间件等，有自己私有的通信协议，以及大量的请求命令类型，此时就需要从头来定制协议。此时将 SOFABolt 用作一个协议框架和具备基础通信功能的组件，比如基础通信模型、连接管理等功能是可以复用的；而协议相关的部分需要自己来开发和实现，可以参考 RPC 协议实现的内容：[示例](https://github.com/sofastack/sofa-bolt/tree/master/src/main/java/com/alipay/remoting/rpc)。