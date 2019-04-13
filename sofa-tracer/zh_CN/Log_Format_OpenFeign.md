# OpenFeign 日志格式

SOFATracer 集成 Spring Cloud OpenFeign 后输出请求的链路数据格式，默认为 `JSON` 数据格式。

### Spring Cloud OpenFeign 摘要日志（feign-digest.log）

以 JSON 格式输出的数据，相应 key 的含义解释如下：

key | 表达含义
--------- | -------------
 time | 日志打印时间
 local.app | 当前应用名
 traceId | TraceId
 spanId | SpanId
 request.url | 请求 URL
 method | 请求 HTTP 方法
 result.code | HTTP 返回状态码
 error | 错误信息
 req.size.bytes | Request Body 大小
 resp.size.bytes | Response Body 大小
 time.cost.milliseconds | 请求耗时（ms）
 current.thread.name | 当前线程名
 remote.host | 目标主机
 remote.port | 目标端口
 component.client.impl | 组件名称
 baggage | 透传的 baggage 数据

样例：

```json
{"time":"2019-03-28 18:08:06.800","local.app":"tracer-consumer","traceId":"0a0fe88f1553767685981100124403","spanId":"0.1","request.url":"http://10.15.232.143:8800/feign","method":"GET","result.code":"200","error":"","req.size.bytes":0,"resp.size.bytes":18,"time.cost.milliseconds":206,"current.thread.name":"http-nio-8082-exec-1","remote.host":"10.15.232.143","remote.port":"8800","component.client.impl":"open-feign","baggage":""}
```

### Spring Cloud OpenFeign 统计日志（feign-stat.log）

`stat.key` 即本段时间内的统计关键字集合，统一关键字集合唯一确定一组统计数据，包含local.app、request.url、和 method 字段.

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
      <td>request.url</td>
      <td>请求 URL</td>
   </tr>
   <tr>
      <td> method </td>
      <td>请求 HTTP 方法</td>
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

```json
{"time":"2019-03-28 18:09:06.800","stat.key":{"request.url":"http://10.15.232.143:8800/feign","local.app":"tracer-consumer","method":"GET"},"count":1,"total.cost.milliseconds":206,"success":"Y","load.test":"F"}
```