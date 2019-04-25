# 名词解释

名词 | 说明
---- | ---
TraceId | TraceId 指的是 SOFATracer 中代表唯一一次请求的 ID，此 ID 一般由集群中第一个处理请求的系统产生，并在分布式调用下通过网络传递到下一个被请求系统。
SpanId |  SpanId 代表了本次请求在整个调用链路中的位置或者说层次，比如 A 系统在处理一个请求的过程中依次调用了 B，C，D 三个系统，那么这三次调用的的 SpanId 分别是：0.1，0.2，0.3。如果 B 系统继续调用了 E，F 两个系统，那么这两次调用的 SpanId 分别是：0.1.1，0.1.2。

其他相关的名词解释可以参考 [OpenTracing 规范](http://opentracing.io/documentation/pages/spec.html)。