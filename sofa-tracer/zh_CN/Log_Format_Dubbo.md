# Dubbo 日志格式

SOFATracer 集成 Dubbo 后输出请求的链路数据格式，默认为 `JSON` 数据格式。

### Dubbo 服务消费方摘要日志（dubbo-client-digest.log）

以 JSON 格式输出的数据，相应 key 的含义解释如下：

key | 表达含义
--------- | -------------
 time | 日志打印时间
 traceId | TraceId
 spanId | SpanId
 span.kind | span 类型
 local.app | 当前应用名
 protocol | 协议
 service | 服务接口
 method | 调用方法
 invoke.type| 调用类型
 remote.host | 目标主机
 remote.port | 目标端口
 local.host | 本地主机
 client.serialize.time | 请求序列化时间
 client.deserialize.time | 响应反序列化时间
 req.size.bytes | Request Body 大小
 resp.size.bytes | Response Body 大小
 result.code | 返回结果状态码
 current.thread.name | 当前线程名
 time.cost.milliseconds | 请求耗时（ms）
 baggage | 透传的 baggage 数据

样例：

```json
{"time":"2019-04-03 11:36:01.909","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"client","local.app":"dubbo-consumer","protocol":"dubbo","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","invoke.type":"sync","remote.host":"10.15.232.69","remote.port":"20880","local.host":"10.15.232.69","client.serialize.time":35,"client.deserialize.time":0,"req.size.bytes":323,"resp.size.bytes":323,"result.code":"00","current.thread.name":"main","time.cost.milliseconds":252,"baggage":""}
```
### Dubbo 服务提供方摘要日志（dubbo-server-digest.log）

以 JSON 格式输出的数据，相应 key 的含义解释如下：

key | 表达含义
--------- | -------------
 time | 日志打印时间
 traceId | TraceId
 spanId | SpanId
 span.kind | span 类型
 local.app | 当前应用名
 service | 服务接口
 method | 调用方法
 local.host | 本地主机
 local.host | 本地端口
 protocol | 协议
 server.serialize.time | 响应序列化时间
 server.deserialize.time | 请求反序列化时间
 result.code | 结果状态码
 current.thread.name | 当前线程名
 time.cost.milliseconds | 请求耗时（ms）
 baggage | 透传的 baggage 数据

样例：

```json
{"time":"2019-04-03 11:36:01.880","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"server","local.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","local.host":"10.15.232.69","local.port":"54178","protocol":"dubbo","server.serialize.time":0,"server.deserialize.time":27,"result.code":"00","current.thread.name":"DubboServerHandler-10.15.232.69:20880-thread-2","time.cost.milliseconds":3,"baggage":""}
```

### Dubbo 统计日志

`stat.key` 即本段时间内的统计关键字集合，统一关键字集合唯一确定一组统计数据，包含local.app、service、和 method 字段.

<table>
   <tr>
      <td colspan="2">key</td>
      <td>表达含义</td>
   </tr>
   <tr>
      <td colspan="2">time</td>
      <td>日志打印时间</td>
   </tr>
   <tr>
      <td rowspan="3">stat.key</td>
      <td>local.app</td>
      <td>当前应用名</td>
   </tr>
   <tr>
      <td>method</td>
      <td>调用方法</td>
   </tr>
   <tr>
      <td> service </td>
      <td>服务名</td>
   </tr>
   <tr>
      <td colspan="2">count</td>
      <td>本段时间内请求次数</td>
   </tr>
   <tr>
      <td colspan="2">total.cost.milliseconds</td>
      <td>本段时间内的请求总耗时（ms）</td>
   </tr>
   <tr>
      <td colspan="2">success</td>
      <td>请求结果：Y 表示成功；N 表示失败</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>压测标记：T 是压测；F 不是压测</td>
   </tr>
</table>

样例：

* dubbo-client-stat.log
```json
{"time":"2019-04-03 11:37:01.650","stat.key":{"method":"SayHello","local.app":"dubbo-consumer","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":252,"success":"Y"}
```

* dubbo-server-stat.log
```json
{"time":"2019-04-03 11:37:01.872","stat.key":{"method":"SayHello","remote.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":3,"success":"Y"}
```