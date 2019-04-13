# RestTemplate 日志格式

SOFATracer 集成 RestTemplate 后输出请求的链路数据格式，默认为 `JSON` 数据格式。

### RestTemplate 摘要日志（resttemplate-digest.log）

以 JSON 格式输出的数据，相应 key 的含义解释如下：

key | 表达含义
--------- | -------------
 time | 日志打印时间
 local.app | 当前应用名
 traceId | TraceId
 spanId | SpanId
 request.url | 请求 URL
 method | 请求 HTTP 方法
 result.code | HTTP 调用返回状态码
 resp.size.bytes | Response Body 大小
 time.cost.milliseconds | 请求耗时（ms）
 current.thread.name | 线程名
 remote.app | 被调用的应用名称
 baggage | 透传的 baggage 数据

样例：

```json
{"time":"2018-10-24 10:45:28.977","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912878910015081","spanId":"0","request.url":"http://sac.alipay.net:8080/rest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":188,"current.thread.name":"main","remote.app":"","baggage":""}
```

### RestTemplate 统计日志（resttemplate-stat.log）

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
{"time":"2018-10-24 10:46:28.769","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/rest"},"count":1,"total.cost.milliseconds":5009,"success":"true","load.test":"F"}
```

