# RestTemplate Log Format
SOFATracer integrates RestTemplate and outputs the requested link log data format. The default is `JSON` data format.

### RestTemplate digest log（resttemplate-digest.log）

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
 resp.size.bytes | Response Body Size
 time.cost.milliseconds | Request time (ms)
 current.thread.name | Current thread name
 remote.app | remote app name
 baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2018-10-24 10:45:28.977","local.app":"RestTemplateDemo","traceId":"0a0fe8b3154034912878910015081","spanId":"0","request.url":"http://sac.alipay.net:8080/rest","method":"GET","result.code":"200","resp.size.bytes":0,"time.cost.milliseconds":188,"current.thread.name":"main","remote.app":"","baggage":""}
```

### RestTemplate stat log（resttemplate-stat.log）

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
{"time":"2018-10-24 10:46:28.769","stat.key":{"method":"GET","local.app":"RestTemplateDemo","request.url":"http://sac.alipay.net:8080/rest"},"count":1,"total.cost.milliseconds":5009,"success":"true","load.test":"F"}
```

