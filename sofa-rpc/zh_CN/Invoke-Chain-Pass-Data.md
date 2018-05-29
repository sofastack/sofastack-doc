## 链路数据透传

链路数据透传功能支持应用向调用上下文中存放数据，达到整个链路上的应用都可以操作该数据。
使用方式如下，可分别向链路的 request 和 response 中放入数据进行透传，并可获取到链路中相应的数据。
```java
RpcInvokeContext.getContext().putRequestBaggage("key_request","value_request");
RpcInvokeContext.getContext().putResponseBaggage("key_response","value_response");

String requestValue=RpcInvokeContext.getContext().getRequestBaggage("key_request");
String responseValue=RpcInvokeContext.getContext().getResponseBaggage("key_response");
```