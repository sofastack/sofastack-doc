# HttpClient Log Format
After integrating tracer-httpclient-plugin, SOFATracer outputs the link data requested by HttpClient in `JSON` data by default.

### HttpClient digest log (httpclient-digest.log)

The data is output in JSON format. Each key meaning is as follows:

Key | Meaning
--------- | -------------
 Time | log printing time
 Local.app | Current application name
 traceId | TraceId
 spanId | SpanId
 Request.url | Request URL
 Method | Request HTTP method
 Result.code | HTTP call returns status code
 req.size.bytes | Request body size
 resp.size.bytes | Response body size
 Time.cost.milliseconds | Request time (ms)
 Current.thread.name | Thread name
 Remote.app | Name of the called application
 Baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2018-09-27 21:58:43.067","local.app":"HttpClientDemo","traceId":"0a0fe8801538056723034100235072","spanId":"0","request.url":"http://localhost:8080/httpclient","method":"GET","result.code":"200","req.size.bytes":0,"resp.size.bytes":-1,"time.cost.milliseconds":33,"current.thread.name":"I/O dispatcher 1","remote.app":"","baggage":""}
```

Note: The application name can be passed in as a parameter when constructing an HttpClient instance via `SofaTracerHttpClientBuilder`.

### HttpClient statistical Log (httpclient-stat.log)

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
      <td>Request result: Y means success (the result code starting with 1 and 2 indicates success, and 302 indicates that the redirection is successful, and others indicate failure); N indicates failure</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>Pressure test mark: T indicates pressure test; F indicates non-pressure test</td>
   </tr>
</table>

Example:

```json
{"time":"2018-09-27 21:59:42.233","stat.key":{"request.url":"http://localhost:8080/httpclient","local.app":"HttpClientDemo","method":"GET"},"count":2,"total.cost.milliseconds":562,"success":"true","load.test":"F"}
```
