# OkHttp Log Format
SOFATracer integrates OkHttp and outputs the requested link log data format. The default is `JSON` data format.

### OkHttp digest log（okhttp-digest.log）

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
 req.size.bytes | Request Body Size
 resp.size.bytes | Response Body Size
 time.cost.milliseconds | Request time (ms)
 current.thread.name | Current thread name
 remote.app | remote app
 baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2019-04-12 13:38:10.187","local.app":"OkHttpDemo","traceId":"0a0fe85a1555047489980100151193","spanId":"0","request.url":"http://localhost:8081/okhttp?name=sofa","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":0,"time.cost.milliseconds":207,"current.thread.name":"main","remote.app":"","baggage":""}
```

### OkHttp stat log（okhttp-stat.log）

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
      <td>Request result: Y means success; N indicates failure</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>Pressure test mark: T indicates pressure test; F indicates non-pressure test</td>
   </tr>
</table>

Example:

```json
{"time":"2019-04-12 13:39:09.720","stat.key":{"method":"GET","local.app":"OkHttpDemo","request.url":"http://localhost:8081/okhttp?name=sofa"},"count":1,"total.cost.milliseconds":207,"success":"true","load.test":"F"}
```