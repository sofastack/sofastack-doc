Since SOFARPC 5.4.0, the SOFATracer function is integrated, which is enabled by default. It can output the data information in the link.

By default, the output data is in `JSON` format. The involved fields are as follows:

## RPC client digest Log (rpc-client-digest.log)

* Log printing time
* TraceId
* SpanId
* Span type
* Current appName
* Protocol type
* Service interface information
* Method name
* Current thread name
* Calling type
* Routing record
* Target IP
* Local machine IP
* Return code
* Request serialization duration
* Response deserialization duration
* Response size (in Byte)
* Request size (in Byte)
* Client connection duration
* Total duration for call
* Local client port
* Transparently transmitted baggage data (kv format)

Example:

```
{"timestamp":"2018-05-20 17:03:20.708","tracerId":"1e27326d1526807000498100185597","spanId":"0","span.kind":"client","local.app":"SOFATracerRPC","protocol":"bolt","service":"com.alipay.sofa.tracer.examples.sofarpc.direct.DirectService:1.0","method":"sayDirect","current.thread.name":"main","invoke.type":"sync","router.record":"DIRECT","remote.ip":"127.0.0.1:12200","local.client.ip":"127.0.0.1","result.code":"00","req.serialize.time":"33","resp.deserialize.time":"39","resp.size":"170","req.size":"582","client.conn.time":"0","client.elapse.time":"155","local.client.port":"59774","baggage":""}
```


## RPC server digest log (rpc-server-digest.log)

* Log printing time
* TraceId
* SpanId
* Span type
* Service interface information
* Method name
* Source IP
* Source appName
* Protocol
* Local appName
* Current thread name
* Return code
* Server thread pool waiting time
* Business processing duration
* Response serialization duration
* Request deserialization duration
* Response size (in Byte)
* Request size (in Byte)
* Transparently transmitted baggage data (kv format)

Example:

```
{"timestamp":"2018-05-20 17:00:53.312","tracerId":"1e27326d1526806853032100185011","spanId":"0","span.kind":"server","service":"com.alipay.sofa.tracer.examples.sofarpc.direct.DirectService:1.0","method":"sayDirect","remote.ip":"127.0.0.1","remote.app":"SOFATracerRPC","protocol":"bolt","local.app":"SOFATracerRPC","current.thread.name":"SOFA-BOLT-BIZ-12200-5-T1","result.code":"00","server.pool.wait.time":"3","biz.impl.time":"0","resp.serialize.time":"4","req.deserialize.time":"38","resp.size":"170","req.size":"582","baggage":"",{"timestamp":"2018-05-20 17:03:05.646","tracerId":"1e27326d1526806985394100185589","spanId":"0","span.kind":"server","service":"com.alipay.sofa.tracer.examples.sofarpc.direct.DirectService:1.0","method":"sayDirect","remote.ip":"127.0.0.1","remote.app":"SOFATracerRPC","protocol":"bolt","local.app":"SOFATracerRPC","current.thread.name":"SOFA-BOLT-BIZ-12200-5-T1","result.code":"00","server.pool.wait.time":"2","biz.impl.time":"1","resp.serialize.time":"1","req.deserialize.time":"6","resp.size":"170","req.size":"582","baggage":"",{"timestamp":"2018-05-20 17:03:20.701","tracerId":"1e27326d1526807000498100185597","spanId":"0","span.kind":"server","service":"com.alipay.sofa.tracer.examples.sofarpc.direct.DirectService:1.0","method":"sayDirect","remote.ip":"127.0.0.1","remote.app":"SOFATracerRPC","protocol":"bolt","local.app":"SOFATracerRPC","current.thread.name":"SOFA-BOLT-BIZ-12200-5-T1","result.code":"00","server.pool.wait.time":"2","biz.impl.time":"0","resp.serialize.time":"1","req.deserialize.time":"4","resp.size":"170","req.size":"582","baggage":"",{"timestamp":"2018-05-20 17:04:19.966","tracerId":"1e27326d1526807046606100185635","spanId":"0","span.kind":"server","service":"com.alipay.sofa.tracer.examples.sofarpc.direct.DirectService:1.0","method":"sayDirect","remote.ip":"127.0.0.1","remote.app":"SOFATracerRPC","protocol":"bolt","local.app":"SOFATracerRPC","current.thread.name":"SOFA-BOLT-BIZ-12200-5-T1","result.code":"00","server.pool.wait.time":"2","biz.impl.time":"0","resp.serialize.time":"1","req.deserialize.time":"4","resp.size":"170","req.size":"582","baggage":""}
```

## RPC client statistical log (rpc-client-stat.log)

* Log printing time
* Log key
* Method information
* Client appName
* Service interface information
* Number of calls
* Total duration
* Calling result

Example:

```
{"time":"2018-05-18 07:02:19.717","stat.key":{"method":"method","local.app":"client","service":"app.service:1.0"},"count":10,"total.cost.milliseconds":17,"success":"Y"}
```



## RPC server statistical log (rpc-server-stat.log)

* Log printing time
* Log key
* Method information
* Client appName
* Service interface information
* Number of calls
* Total duration
* Calling result

Example:

```
{"time":"2018-05-18 07:02:19.717","stat.key":{"method":"method","local.app":"client","service":"app.service:1.0"},"count":10,"total.cost.milliseconds":17,"success":"Y"}
```