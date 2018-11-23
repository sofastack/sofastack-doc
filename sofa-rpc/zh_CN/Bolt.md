## Bolt 协议

Bolt 协议一个基于 TCP 的自定义的协议，相比 HTTP 来说，性能更好，在蚂蚁金服内部，大量的 RPC 都是采用 Bolt 协议来进行通信：

* [基本使用](./Bolt_Usage.md)
* [调用方式](./Invoke-Type)
* [超时控制](./Bolt_Timeout.md)
* [泛化调用](./Generic-Invoke.md)
* [序列化协议](./Serialization.md)
* [自定义线程池](./Custom-ThreadPool.md)