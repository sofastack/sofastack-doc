# SOFATracer 工具类

## 通过 SOFATracer 上下文获取 Span

在一次分布式链路调用过程中，在集成了 SOFATracer 的组件会产生一个 Span 并会缓存到 SOFATracer 的上下文中，这个上下文是缓存在 ThreadLocal 中的，作为使用者可以通过如下的方式随时获取到当前 SOFATracer 的上下文：

```java
SofaTraceContext sofaTraceContext = SofaTraceContextHolder.getSofaTraceContext();
```

SOFATracer 上下文 `SofaTraceContext` 通过这个实例，可以对其缓存的 Span 执行增、删、改、查和清空操作。作为组件集成的同学在集成过程中我们会对 SOFATracer 上下文做增、删、改和查等操作来集成分布式链路跟踪的能力；但是作为直接使用 SOFATracer 的应用开发者，我们只需要能够获取相应的 Span 即可，即只需要在获取上下文后使用如下的方法：

```
SofaTracerSpan sofaTracerSpan = sofaTraceContext.getCurrentSpan();
```

## 通过 Span 获取信息

在使用相应的组件如 Spring MVC 时，该组件集成了 SOFATracer 的能力后可以在获取到 Span 后获取到 Span 中的所有信息，具体获取方式示例（前提 Span 不为空即相应组件已经集成 SOFATracer）：

### 获取 TraceId 和 SpanId ：

```java
SofaTracerSpanContext sofaTracerSpanContext = currentSpan.getSofaTracerSpanContext();
String traceId = sofaTracerSpanContext.getTraceId();
String spanId = sofaTracerSpanContext.getSpanId();
```

### 获取 OpenTracing 规范中的 `Tags` 和 `Logs`

获取 `Tags`:

```java
Map<String, String>    tagsStr = sofaTracerSpan.getTagsWithStr();
Map<String, Boolean>  tagsBool = sofaTracerSpan.getTagsWithBool();
Map<String, Number> tagsNumber = sofaTracerSpan.getTagsWithNumber();
```

获取 `Logs`:

```java
List<LogData> logDataList = sofaTracerSpan.getLogs();
```

## 透传数据处理

Baggage 元素是一个键值对集合，其携带的是需要透传的数据。SOFATracer 中将 Baggage 数据分为 sysBaggage 和 bizBaggage；sysBaggage 主要是指系统维度的透传数据，bizBaggage 主要是指业务的透传数据。

### 设置和获取 BaggageItem 

BaggageItem 是 Baggage集合中的数据元素。

1、通过标准接口设置相应的 BaggageItem 数据：

```java
String baggageKey = "key";
String baggageVal = "val";
sofaTracerSpan.setBaggageItem(baggageKey,baggageVal);
```

2、通过标准接口获取相应的 BaggageItem 数据：

```java
String baggageKey = "key";
String baggageValue = sofaTracerSpan.getBaggageItem(baggageKey);
```

注：当通过标准接口进行设置和获取 Baggage 数据时，实际上操作的对象均为 bizBaggage

### 设置和获取 'Baggage' 数据

1、设置 'Baggage' 数据

```java
SofaTracerSpanContext sofaTracerSpanContext = sofaTracerSpan.getSofaTracerSpanContext();

Map<String, String> bizBaggage = new HashMap<String, String>();
bizBaggage.put("bizKey","bizVal");
sofaTracerSpanContext.addBizBaggage(bizBaggage);

Map<String, String> sysBaggage = new HashMap<String, String>();
sysBaggage.put("sysKey","sysVal");
sofaTracerSpanContext.addSysBaggage(sysBaggage);
```

2、获取 'Baggage' 数据

```java
SofaTracerSpanContext sofaTracerSpanContext = sofaTracerSpan.getSofaTracerSpanContext();
//获取 bizBaggage
Map<String, String> bizBaggages = sofaTracerSpanContext.getBizBaggage();
//获取 sysBaggage
Map<String, String> sysBaggages = sofaTracerSpanContext.getSysBaggage();
```

### 遍历 Baggage 数据

OpenTracing 规范中 SpanContext 接口提供了 baggageItems() 方法，可以通过这个方法来遍历所有的 baggage 元素。SOFATracer 在 SofaTracerSpanContext 类中对 baggageItems() 方法进行了具体实现。

```java
Iterable<Map.Entry<String, String>> entrySet = sofaTracerSpanContext.baggageItems();
```

注：遍历 Baggage 数据返回的是 sysBaggage 和 bizBaggage 的合集。


