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