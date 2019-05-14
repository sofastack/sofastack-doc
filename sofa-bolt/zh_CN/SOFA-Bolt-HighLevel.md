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