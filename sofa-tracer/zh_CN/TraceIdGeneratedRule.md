# TraceId 和 SpanId 生成规则

### TraceId 生成规则

SOFATracer 通过 TraceId 来将一个请求在各个服务器上的调用日志串联起来，TraceId 一般由接收请求经过的第一个服务器产生，产生规则是： 服务器 IP + 产生 ID 时候的时间 + 自增序列 + 当前进程号 ，比如：

```
0ad1348f1403169275002100356696
```

前 8 位 `0ad1348f` 即产生 `TraceId` 的机器的 IP，这是一个十六进制的数字，每两位代表 IP 中的一段，我们把这个数字，按每两位转成 10 进制即可得到常见的 IP 地址表示方式 `10.209.52.143`，大家也可以根据这个规律来查找到请求经过的第一个服务器。
后面的 13 位 `1403169275002` 是产生 TraceId 的时间。
之后的 4 位 `1003` 是一个自增的序列，从 1000 涨到 9000，到达 9000 后回到 1000 再开始往上涨。
最后的 5 位 `56696` 是当前的进程 ID，为了防止单机多进程出现 TraceId 冲突的情况，所以在 TraceId 末尾添加了当前的进程 ID。

> TraceId 目前的生成的规则参考了阿里的鹰眼组件。 


### SpanId 生成规则

SOFATracer 中的 SpanId 代表本次调用在整个调用链路树中的位置，假设一个 Web 系统 A 接收了一次用户请求，那么在这个系统的 SOFATracer MVC 日志中，记录下的 SpanId 是 0，代表是整个调用的根节点，如果 A 系统处理这次请求，需要通过 RPC 依次调用 B，C，D 三个系统，那么在 A 系统的 SOFATracer RPC 客户端日志中，SpanId 分别是 0.1，0.2 和 0.3，在 B，C，D 三个系统的 SOFATracer RPC 服务端日志中，SpanId 也分别是 0.1，0.2 和 0.3；如果 C 系统在处理请求的时候又调用了 E，F 两个系统，那么 C 系统中对应的 SOFATracer RPC 客户端日志是 0.2.1 和 0.2.2，E，F 两个系统对应的 SOFATracer RPC 服务端日志也是 0.2.1 和 0.2.2。根据上面的描述，我们可以知道，如果把一次调用中所有的 SpanId 收集起来，可以组成一棵完整的链路树。

我们假设一次分布式调用中产生的 TraceId 是 `0a1234`（实际不会这么短），那么根据上文 SpanId 的产生过程，有下图：
<center>

![traceId](https://gw.alipayobjects.com/mdn/rms_432828/afts/img/A*qo08QLrjv-QAAAAAAAAAAABjARQnAQ) 

</center>

> SpanId 目前的生成的规则参考了阿里的鹰眼组件。 

