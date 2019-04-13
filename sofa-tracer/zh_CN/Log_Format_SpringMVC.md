# SpringMVC 日志格式

SOFATracer 集成 SpringMVC 后输出 MVC 请求的链路数据格式，默认为 `JSON` 数据格式。

### Spring MVC 摘要日志（spring-mvc-digest.log）

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
 req.size.bytes | Request Body 大小
 resp.size.bytes | Response Body 大小
 time.cost.milliseconds | 请求耗时（ms）
 current.thread.name | 当前线程名
 baggage | 透传的 baggage 数据

样例：

```json
{"time":"2018-06-03 16:44:05.829","local.app":"SpringMvcJsonOutput","traceId":"c0a80d9e1528015445828101064625","spanId":"0","request.url":"http://localhost:63933/greeting","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":50,"time.cost.milliseconds":1,"current.thread.name":"http-nio-auto-1-exec-10","baggage":""}
```

### Spring MVC 统计日志（spring-mvc-stat.log）

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
      <td>请求结果：Y 表示成功(1 开头和 2 开头的结果码算是成功的，302表示的重定向算成功，其他算是失败的)；N 表示失败</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>压测标记：T 是压测；F 不是压测</td>
   </tr>
</table>

样例：

```json
{"time":"2018-06-03 16:44:02.473","stat.key":{"request.url":"http://localhost:63933/greeting","local.app":"SpringMvcJsonOutput","method":"GET"},"count":5,"total.cost.milliseconds":149,"success":"Y","load.test":"F"}
```