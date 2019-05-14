## 链路数据透传

链路数据透传功能支持应用向调用上下文中存放数据，达到整个链路上的应用都可以操作该数据。
使用方式如下，可分别向链路的 request 和 response 中放入数据进行透传，并可获取到链路中相应的数据。
```java
RpcInvokeContext.getContext().putRequestBaggage("key_request","value_request");
RpcInvokeContext.getContext().putResponseBaggage("key_response","value_response");

String requestValue=RpcInvokeContext.getContext().getRequestBaggage("key_request");
String responseValue=RpcInvokeContext.getContext().getResponseBaggage("key_response");
```


## 使用示例

例如 A -> B -> C 的场景中，将 A 设置的请求隐式传参数据传递给 B 和 C。在返回的时候，将 C 和 B 的响应隐式传参数据传递给 A。

A 请求方设置的时候：
```java
// 调用前设置请求透传的值
RpcInvokeContext context = RpcInvokeContext.getContext();
context.putRequestBaggage("reqBaggageB", "a2bbb");
// 调用
String result = service.hello();
// 拿到结果透传的值
context.getResponseBaggage("respBaggageB");
```

B 业务代码中：
```java
public String hello() {
    // 拿到请求透传的值
    RpcInvokeContext context = RpcInvokeContext.getContext();
    String reqBaggage = context.getRequestBaggage("reqBaggageB");
    // 
    doSomthing();
    // 结果透传一个值
    context.putResponseBaggage("respBaggageB", "b2aaa");
    return result;
}
```

如果中途自己启动了子线程，则需要设置子线程的上下文：
```java
CountDownLatch latch = new CountDownLatch(1);
final RpcInvokeContext parentContext = RpcInvokeContext.peekContext();
Thread thread = new Thread(new Runnable(){
    public void run(){
	  try {
	      RpcInvokeContext.setContext(parentContext);
		  // 调一个远程服务
		  xxxService.sayHello();
		  latch.countDown();
	  } finally {
	      RpcInvokeContext.removeContext();
	  }
    }
}, "new-thread");
thread.start();

// 此时拿不到返回值透传的数据的
latch.await(); //等待
// 此时返回结束，能拿到返回透传的值
```

## 和 SOFATracer 的比较

[SOFATracer](https://github.com/sofastack/sofa-tracer/wiki) 是蚂蚁开源的一个分布式链路追踪系统,RPC 目前已经和 Tracer 做了集成,默认开启.
和 Tracer 进行数据传递不同的是

1. RPC的数据透传更偏向业务使用,而且可以在全链路中进行双向传递,调用方可以传给服务方,服务方也可以传递信息给调用方,SOFATracer 更加偏向于中间件和业务无感知的数据的传递,只能进行单向传递.
2. RPC的透传可以选择性地不在全链路中透传,而Tracer 中如果传递大量信息,会在整个链路中传递.可能对下游业务会有影响.

所以整体来看,两个信息各有利弊,在有一些和业务相关的透传数据的情况下,可以选择 RPC 的透传.