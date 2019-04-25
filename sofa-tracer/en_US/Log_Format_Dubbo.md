# Dubbo Log Format
SOFATracer integrates Dubbo and outputs the requested link log data format. The default is `JSON` data format.

### Dubbo service consumer digest log（dubbo-client-digest.log）

The data is output in JSON format. Each key meaning is as follows:

key | Meaning
--------- | -------------
 time | Log printing time
 traceId | TraceId
 spanId | SpanId
 span.kind | span Type
 local.app | Current application name
 protocol | protocol
 service | service interface
 method | service method
 invoke.type| invoke type
 remote.host | remote host
 remote.port | remote port
 local.host | local host
 client.serialize.time | request serialize time
 client.deserialize.time | response deserialize time
 req.size.bytes | Request Body Size
 resp.size.bytes | Response Body Size
 result.code | result code
 current.thread.name | Current thread name
 time.cost.milliseconds | Request time (ms)
 baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2019-04-03 11:36:01.909","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"client","local.app":"dubbo-consumer","protocol":"dubbo","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","invoke.type":"sync","remote.host":"10.15.232.69","remote.port":"20880","local.host":"10.15.232.69","client.serialize.time":35,"client.deserialize.time":0,"req.size.bytes":323,"resp.size.bytes":323,"result.code":"00","current.thread.name":"main","time.cost.milliseconds":252,"baggage":""}
```
### Dubbo service provider digest log（dubbo-server-digest.log）

The data is output in JSON format. Each key meaning is as follows:

key | Meaning
--------- | -------------
 time | Log printing time
 traceId | TraceId
 spanId | SpanId
 span.kind | span Type
 local.app | current application name
 service | service inteface
 method | service method
 local.host | local host
 local.host | local port
 protocol | protocol
 server.serialize.time | response serialize time
 server.deserialize.time | request deserialize time
 result.code | result code
 current.thread.name | current thread name
 time.cost.milliseconds | Request time (ms)
 baggage | Transparently transmitted baggage data

Example

```json
{"time":"2019-04-03 11:36:01.880","traceId":"0a0fe8451554262561656100126684","spanId":"0","span.kind":"server","local.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService","method":"SayHello","local.host":"10.15.232.69","local.port":"54178","protocol":"dubbo","server.serialize.time":0,"server.deserialize.time":27,"result.code":"00","current.thread.name":"DubboServerHandler-10.15.232.69:20880-thread-2","time.cost.milliseconds":3,"baggage":""}
```

### Dubbo stat
`stat.key` is the collection of statistical keywords in this period, which uniquely determines a set of statistical data, including local.app, service, and method field.

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
      <td>service</td>
      <td>service interface</td>
   </tr>
   <tr>
      <td> method </td>
      <td>service method</td>
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

* dubbo-client-stat.log
```json
{"time":"2019-04-03 11:37:01.650","stat.key":{"method":"SayHello","local.app":"dubbo-consumer","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":252,"success":"Y"}
```

* dubbo-server-stat.log
```json
{"time":"2019-04-03 11:37:01.872","stat.key":{"method":"SayHello","remote.app":"dubbo-provider","service":"com.alipay.sofa.tracer.examples.dubbo.facade.HelloService"},"count":1,"total.cost.milliseconds":3,"success":"Y"}
```