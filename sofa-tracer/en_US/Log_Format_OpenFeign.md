# OpenFeign Log Format
SOFATracer integrates Spring Cloud OpenFeign and outputs the requested link log data format. The default is `JSON` data format.

### Spring Cloud OpenFeign digest log（feign-digest.log）

The data is output in JSON format. Each key meaning is as follows:

key | Meaning
--------- | -------------
 time | Log printing time
 local.app | Current application name
 traceId | TraceId
 spanId | SpanId
 request.url | Request URL
 method | Request HTTP method
 result.code | HTTP return status code
 error | error massage
 req.size.bytes | Request Body Size
 resp.size.bytes | Response Body Size
 time.cost.milliseconds | Request time (ms)
 current.thread.name | Current thread name
 remote.host | remote host
 remote.port | remote port
 component.client.impl | component name
 baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2019-03-28 18:08:06.800","local.app":"tracer-consumer","traceId":"0a0fe88f1553767685981100124403","spanId":"0.1","request.url":"http://10.15.232.143:8800/feign","method":"GET","result.code":"200","error":"","req.size.bytes":0,"resp.size.bytes":18,"time.cost.milliseconds":206,"current.thread.name":"http-nio-8082-exec-1","remote.host":"10.15.232.143","remote.port":"8800","component.client.impl":"open-feign","baggage":""}
```

### Spring Cloud OpenFeign stat log（feign-stat.log）

`stat.key` is the collection of statistical keywords in this period, which uniquely determines a set of statistical data, including local.app, request.url, and method field.

<table>
   <tr>
      <td colspan="2">Key</td>
      <td>Meaning</td>
   </tr>
   <tr>
      <td colspan="2">time</td>
      <td>Log printing time</td>
   </tr>
   <tr>
      <td rowspan="3">stat.key</td>
      <td>local.app</td>
      <td>Current application name</td>
   </tr>
   <tr>
      <td>request.url</td>
      <td>Request URL</td>
   </tr>
   <tr>
      <td> method </td>
      <td>Request HTTP method</td>
   </tr>
   <tr>
      <td colspan="2">count</td>
      <td>Number of requests in this period</td>
   </tr>
   <tr>
      <td colspan="2">total.cost.milliseconds</td>
      <td>Total duration (ms) for requests in this period</td>
   </tr>
   <tr>
      <td colspan="2">success</td>
      <td>Request result: Y means success ; N indicates failure</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>Pressure test mark: T indicates pressure test; F indicates non-pressure test</td>
   </tr>
</table>

Example:

```json
{"time":"2019-03-28 18:09:06.800","stat.key":{"request.url":"http://10.15.232.143:8800/feign","local.app":"tracer-consumer","method":"GET"},"count":1,"total.cost.milliseconds":206,"success":"Y","load.test":"F"}
```