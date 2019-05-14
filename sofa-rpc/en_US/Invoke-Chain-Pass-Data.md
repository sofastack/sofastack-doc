## Link data transparent transmission

The link data transparent transmission function allows the applications to store data in the calling context, and then any applications in the entire link can operate the data.
This feature is used as follows. Data can be put into the request and response of the link for transparent transmission, and then the applications can get the corresponding data from the link.
```java
RpcInvokeContext.getContext().putRequestBaggage("key_request","value_request");
RpcInvokeContext.getContext().putResponseBaggage("key_response","value_response");

String requestValue=RpcInvokeContext.getContext().getRequestBaggage("key_request");
String responseValue=RpcInvokeContext.getContext().getResponseBaggage("key_response");
```


## Example

For example, in the scenario of A -> B -> C, the request arguments set by A are transmitted to B and C. On return, response arguments of C and B are transmitted to A.

Requester A is set as follows:
```java
// Set the value of the request transparently before calling
RpcInvokeContext context = RpcInvokeContext.getContext();
context.putRequestBaggage("reqBaggageB", "a2bbb");
// Call service
String result = service.hello();
// Get the result value
context.getResponseBaggage("respBaggageB");
```

Business code for B is as follows:
```java
public String hello() {
    / / Get the value of the request transparent transmission
    RpcInvokeContext context = RpcInvokeContext.getContext();
    String reqBaggage = context.getRequestBaggage("reqBaggageB");
    // 
    doSomthing();
    // result passes a value transparently
    context.putResponseBaggage("respBaggageB", "b2aaa");
    return result;
}
```

If you start the child thread halfway, you need to set the context of the child thread:
```java
CountDownLatch latch = new CountDownLatch(1);
final RpcInvokeContext parentContext = RpcInvokeContext.peekContext();
Thread thread = new Thread(new Runnable(){
    public void run(){
	  Try {
	      RpcInvokeContext.setContext(parentContext);
		  / / Call a remote service
		  xxxService.sayHello();
		  latch.countDown();
	  } finally {
	      RpcInvokeContext.removeContext();
	  }
    }
}, "new-thread");
thread.start();

// If failed to get the transparently transmitted data of the return value.
latch.await(); //wait
// Return ends, and you can get the value returned by transparent transmission.
```

## Compare with SOFATracer

[SOFATracer](https://github.com/sofastack/sofa-tracer/wiki) is an open-source distributed link tracing system of Ant Finanicial. RPC has been integrated with Tracer and is enabled by default.

The differences between data transparent transmission and data transfer by Tracer are as follows:

1. RPC data transparent transmission is business-oriented. And it can implement two-way data transmission in the full link. The caller can transmit data to the service provider, and the service provider can also transmit data to the caller. SOFATracer is middleware-oriented and is more suitable for the data transfer without service itself perceving. It can only implement one-way data transmission.
2. The transparent transmission of RPC can select not to transparently transmit data in the full link. If there is a lot of information to be transmitted in Tracer, it will be transmitted throughout the link, which may affect downstream businesses.

So overall, the two methods have their own advantages and disadvantages. In face of some business-related transparently transmitted data, you can select RPC transparent transmission.