# SOFATracer Tools

## Get Span through SOFATracer context

In the process of a distributed link call, the component that integrates SOFATracer generates a Span and caches it in the SOFATracer context. And the context is cached in ThreadLocal. You can get the current SOFATracer context in the following way:

```java
SofaTraceContext sofaTraceContext = SofaTraceContextHolder.getSofaTraceContext();
```

Through the SOFATracer context `SofaTraceContext`, you can add, delete, modify, check, and empty the cached Spans. As the developers responsible for integrating components, we will add, delete, modify and check the SOFATracer context to integrate distributed link tracking. However, as the application developer to directly use SOFATracer, you only need to get the corresponding Span. That is to say, you only need to use the following method after getting the context:

```java
SofaTracerSpan sofaTracerSpan = sofaTraceContext.getCurrentSpan();
```

## Get information through Span

When using the SOFATracer plugin component, such as Spring MVC, the component integrates the capabilities of SOFATracer. So it can get all the information in the Span after getting Span. The specific acquisition method example (it demands that Span is not empty, namely that the corresponding component has integrated SOFATracer) is as follow:

### Get TraceId and SpanId:

```java
SofaTracerSpanContext sofaTracerSpanContext = currentSpan.getSofaTracerSpanContext();
String traceId = sofaTracerSpanContext.getTraceId();
String spanId = sofaTracerSpanContext.getSpanId();
```

### Get `Tags` and `Logs` in OpenTracing specification

Get `Tags`:

```java
Map<String, String>    tagsStr = sofaTracerSpan.getTagsWithStr();
Map<String, Boolean>  tagsBool = sofaTracerSpan.getTagsWithBool();
Map<String, Number> tagsNumber = sofaTracerSpan.getTagsWithNumber();
```

Get `Logs`:

```java
List <LogData> logDataList = sofaTracerSpan.getLogs ();
```

## Process transparently transmitted data

Baggage element is a collection of key-value pairs that carries data to be transparently transmitted. In SOFATracer, Baggage data is divided into sysBaggage and bizBaggage; sysBaggage mainly refers to transparently transmitted system data, and bizBaggage mainly refers to transparently transmitted business data.

### Configure and get BaggageItem 

BaggageItem is a data element in the Baggage collection.

1. Configure the corresponding BaggageItem data through the standard interface:

```java
String baggageKey = "key";
String baggageVal = "val";
sofaTracerSpan.setBaggageItem(baggageKey,baggageVal);
```

2. Get the corresponding BaggageItem data through the standard interface:

```java
String baggageKey = "key";
String baggageValue = sofaTracerSpan.getBaggageItem(baggageKey);
```

Note: Configuring and getting Baggage data through the standard interface is actually operated on bizBaggage.

### Configure and get 'Baggage' data

1, Configure 'Baggage' data

```java
SofaTracerSpanContext sofaTracerSpanContext = sofaTracerSpan.getSofaTracerSpanContext();

Map<String, String> bizBaggage = new HashMap<String, String>();
bizBaggage.put("bizKey","bizVal");
sofaTracerSpanContext.addBizBaggage(bizBaggage);

Map<String, String> sysBaggage = new HashMap<String, String>();
sysBaggage.put("sysKey","sysVal");
sofaTracerSpanContext.addSysBaggage(sysBaggage);
```

2, Get 'Baggage' data

```java
SofaTracerSpanContext sofaTracerSpanContext = sofaTracerSpan.getSofaTracerSpanContext();
//Get bizBaggage
Map<String, String> bizBaggages = sofaTracerSpanContext.getBizBaggage();
/ / Get sysBaggage
Map<String, String> sysBaggages = sofaTracerSpanContext.getSysBaggage();
```

### Traverse Baggage data

The SpanContext interface in OpenTracing specification provides baggageItems() method that can be used to loop through all baggage elements. SOFATracer implements the baggageItems() method in the SofaTracerSpanContext class.

```java
Iterable<Map.Entry<String, String>> entrySet = sofaTracerSpanContext.baggageItems();
```

Note: Traversing Baggage data returns a collection of sysBaggage and bizBaggage.