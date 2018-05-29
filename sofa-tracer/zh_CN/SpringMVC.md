SOFATracer 集成在 SpringMVC 后输出链路数据的格式，默认为 `json` 数据格式，当配置 `com.alipay.sofa.tracer.springmvc.jsonOutput=false` 时，SpringMVC 的链路数据是以逗号 `,` 分隔的格式输出，具体的字段含义解释如下：

## Spring MVC 摘要日志（spring-mvc-digest.log）

* 日志打印时间
* 当前应用名
* TraceId
* SpanId
* 请求 URL
* 请求 HTTP 方法
* HTTP 返回状态码
* Request Body 大小
* Response Body 大小
* 请求耗时（ms）
* 当前线程名
* 透传的 baggage 数据 (kv 格式)

样例：

```
2018-05-11 10:00:19.610,zipkin-client,0a0fe9161526004019485100164905,0,http://localhost:62656/greeting,GET,200,0B,49B,125ms,http-nio-auto-1-exec-1,baggageKey=baggageValue&
```

## Spring MVC 统计日志（spring-mvc-stat.log）

* 日志打印时间
* 当前应用名
* 请求 URL
* 请求方法
* 本段时间内请求次数
* 本段时间内的请求总耗时
* 请求结果：`Y` 表示成功(1 开头和 2 开头的结果码算是成功的，302表示的重定向算成功，其他算是失败的)；`N` 表示失败
* 压测标记：`T` 是压测；`F` 不是压测

样例：

```
2018-05-11 10:32:58.743,zipkin-client,http://localhost:63181/greeting,GET,1,101,Y,F
``` 