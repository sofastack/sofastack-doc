# Print traceId And spanId To Application Log

SLF4J provides MDC (Mapped Diagnostic Contexts), which supports you to define and modify log output formats and content. This document introduces the  SLF4J MDC feature integrated in SOFATracer, which allows you to output the current SOFATracer context `TraceId` and `SpanId` with simply modifying the log configuration file.

## Prerequisites

In order to properly print the `TraceId` and `SpanId` parameters in the logs of the application, the log programming interface needs to be programmed for [`SLF4J`]((https://www.slf4j.org/manual.html)). That is, the programming interface for printing log does not rely on specific log implementation.

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>
```

## Introduce dependency

For SOFABoot or Spring Boot application, you need to introduce the specific log implementation. It is recommended to introduce Logback and Log4j2 instead of Log4j. Also, it is suggested to use only one log implementation rather than multiple implementations.

* Logback implementation:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

* Log4j2 implementation:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
    <!--SOFABoot does not control log4j2 version -->
    <version>1.4.2.RELEASE</version>
</dependency>
```

## Configuration method

The corresponding TraceId and SpanId are printed based on [SLF4J MDC](https://www.slf4j.org/manual.html). First, the log programming interface in application should be oriented to `SLF4J`, as follows:

```java
/ / Introduce interface
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
	
/ / Construct log printing instance
private static final Logger logger = LoggerFactory.getLogger(XXX.class);
```

Second, to correctly print the `TraceId` and `SpanId` parameters, we also need to configure the extra parameters of `PatternLayout` in the log configuration file. The two parameters are `%X{SOFA-TraceId}` and `%X. {SOFA-SpanId}`, whose values ​​can be obtained from MDC. 

[ `pattern` parameter configured with `Logback` as an example](https://logback.qos.ch/):

```xml
<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %5p  [%X{SOFA-TraceId},
%X{SOFA-SpanId}] 
---- %m%n</pattern>
```

* Key configuration items: As a part of the `Logback pattern`, `[%X{SOFA-TraceId},%X{SOFA-SpanId}]` replaces the placeholders in the pattern with the specific TraceId and SpanId in the current thread process when the corresponding `appender` is called. If there is no corresponding TraceId and SpanId in the current thread, the placeholder is replaced with "null".

[Log4j2 PatternLayout configuration sample](https://logging.apache.org/log4j/2.0/manual/layouts.html):

```xml
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} %5p 
[%X{SOFA-TraceId},%X{SOFA-SpanId}] ---- %m%n " />
```


[Log4j PatternLayout configuration sample](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html):

```xml
 <layout class="org.apache.log4j.PatternLayout">
     <param name="ConversionPattern" value="%d %-5p %-32t 
     [%X{SOFA-TraceId},%X{SOFA-SpanId}] - %m%n"/>
 </layout>
```


> Note: `[%X{SOFA-TraceId},%X{SOFA-SpanId}]` is the recommended printing format, which can be customized based on your actual scenario requirements.

Attachment: [Source code](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-slf4j) based on Logback sample project.

