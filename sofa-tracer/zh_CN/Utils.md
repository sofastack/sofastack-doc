## 一、通过 SOFATracer 上下文获取 Span

在一次分布式链路调用过程中，在集成了 SOFATracer 的组件会产生一个 Span 并会缓存到 SOFATracer 的上下文中，这个上下文是缓存在 ThreadLocal 中的，作为使用者可以通过如下的方式随时获取到当前 SOFATracer 的上下文：

```java
SofaTraceContext sofaTraceContext = SofaTraceContextHolder.getSofaTraceContext();
```

SOFATracer 上下文 `SofaTraceContext` 通过这个实例，可以对其缓存的 Span 执行增、删、改、查和清空操作。作为组件集成的同学在集成过程中我们会对 SOFATracer 上下文做增、删、改和查等操作来集成分布式链路跟踪的能力；但是作为直接使用 SOFATracer 的应用开发者，我们只需要能够获取相应的 Span 即可，即只需要在获取上下文后使用如下的方法：

```
SofaTracerSpan sofaTracerSpan = sofaTraceContext.getCurrentSpan();
```

## 二、通过 Span 获取信息

在使用相应的组件如 Spring MVC 时，该组件集成了 SOFATracer 的能力后可以在获取到 Span 后获取到 Span 中的所有信息，具体获取方式示例（前提 Span 不为空即相应组件已经集成 SOFATracer）：

1. 获取 TraceId 和 SpanId ：

```java
SofaTracerSpanContext sofaTracerSpanContext = currentSpan.getSofaTracerSpanContext();
String traceId = sofaTracerSpanContext.getTraceId();
String spanId = sofaTracerSpanContext.getSpanId();
```

2. 获取 OpenTracing 规范中的 `Baggage`

通过标准接口即可获取相应的 `Baggage` 数据：

```java
String baggageKey = "key";
String baggageValue = sofaTracerSpan.getBaggageItem(baggageKey);
```

3. 获取 OpenTracing 规范中的 `Tags` 和 `Logs`

获取 `Tags`:

```java
Map<String, String> tagsStr = sofaTracerSpan.getTagsWithStr();
Map<String, Boolean> tagsBool = sofaTracerSpan.getTagsWithBool();
Map<String, Number> tagsNumber = sofaTracerSpan.getTagsWithNumber();
```

获取 `Logs`:

```
List<LogData> logDataList = sofaTracerSpan.getLogs();
```



