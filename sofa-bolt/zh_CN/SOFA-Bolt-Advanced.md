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