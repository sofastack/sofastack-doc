# SpringMVC Log Format
After integrating SpringMVC, SOFATracer will output the link data format of the MVC requests, which is `JSON` by default.

### Spring MVC digest log (spring-mvc-digest.log)

Data is ouput in JSON format. The meaning of each key is as follows:

Key | Meaning
--------- | -------------
 Time | Log printing time
 Local.app | Current application name
 traceId | TraceId
 spanId | SpanId
 Request.url | Request URL
 Method | Request HTTP method
 Result.code | HTTP return status code
 req.size.bytes | Request body size
 resp.size.bytes | Response body size
 Time.cost.milliseconds | Request time (ms)
 Current.thread.name | Current thread name
 Baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2018-06-03 16:44:05.829","local.app":"SpringMvcJsonOutput","traceId":"c0a80d9e1528015445828101064625","spanId":"0","request.url":"http://localhost:63933/greeting","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":50,"time.cost.milliseconds":1,"current.thread.name":"http-nio-auto-1-exec-10","baggage":""}
```

### Spring MVC statistical log (spring-mvc-stat.log)

`stat.key` is a collection of statistical keywords in this period., which uniquely determines a set of statistical data, including local.app, request.url, and method field.

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
      <td>Total duration for (ms) requests in this period</td>
   </tr>
   <tr>
      <td colspan="2">success</td>
      <td>Request result: Y means success (the result code starting with 1 and 2 indicates success, and 302 indicates that the redirection is successful, and others indicate failure); N indicates failure</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>Pressure test mark: T indicates pressure test; F indicates non-pressure test</td>
   </tr>
</table>

Example:

```json
{"time":"2018-06-03 16:44:02.473","stat.key":{"request.url":"http://localhost:63933/greeting","local.app":"SpringMvcJsonOutput","method":"GET"},"count":5,"total.cost.milliseconds":149,"success":"Y","load.test":"F"}
```