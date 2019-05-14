# 应用日志中打印 traceId 和 spanId

SLF4J 提供了 MDC （Mapped Diagnostic Contexts）功能，可以支持用户定义和修改日志的输出格式以及内容。本文将介绍 SOFATracer 集成的 SLF4J MDC功能，方便用户在只简单修改日志配置文件的前提下输出当前 SOFATracer 上下文 `TraceId` 以及 `SpanId` 。

## 使用前提

为了在应用中的日志正确打印 `TraceId` 和 `SpanId` 参数，我们的日志编程接口需要面向 [`SLF4J`]((https://www.slf4j.org/manual.html)) 进行编程，即打印日志的编程接口不要依赖具体的日志实现。

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>
```

## 引入依赖

如果是 SOFABoot 或者 Spring Boot 的应用具体的日志实现需要大家去引入，我们推荐的日志打印实现是 Logback 和 Log4j2，不推荐 Log4j，同时日志实现建议只使用一个而不要使用多个实现。

* Logback 实现引入：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```
* Log4j2 实现引入：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
    <!--SOFABoot 没有管控 log4j2 版本 -->
    <version>1.4.2.RELEASE</version>
</dependency>
```

## 配置方法

我们基于 [SLF4J MDC](https://www.slf4j.org/manual.html) 的原理打印对应的 TraceId 和 SpanId，首先我们的应用中的日志编程接口应该面向 `SLF4J`，如通过如下的方式：

```java
//引入接口
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
	
//构造日志打印实例
private static final Logger logger = LoggerFactory.getLogger(XXX.class);
```

其次，我们为了正确打印 `TraceId` 和 `SpanId` 参数，我们还需要在日志的配置文件中配置 `PatternLayout` 的额外参数，这两个参数是 `%X{SOFA-TraceId}` 和 `%X{SOFA-SpanId}`，参数值我们均是从 MDC 中获取的值。 

[以 `Logback` 为例配置的 `pattern` 参数](https://logback.qos.ch/)：

```xml
<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %5p  [%X{SOFA-TraceId},
%X{SOFA-SpanId}] 
---- %m%n</pattern>
```

* 关键配置项目：`[%X{SOFA-TraceId},%X{SOFA-SpanId}]` 作为 `Logback pattern` 的一部分，在对应的 `appender` 被调用的时候，会根据 pattern 中的占位符替换为当前线程上下文中 TraceId 和 SpanId 的具体值，当前线程中没有对应的 TraceId 和 SpanId 值时，会用“空字符串”替代。

[Log4j2 配置 PatternLayout 样例](https://logging.apache.org/log4j/2.0/manual/layouts.html)：

```xml
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} %5p 
[%X{SOFA-TraceId},%X{SOFA-SpanId}] ---- %m%n " />
```
[Log4j 配置 PatternLayout 样例](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html)：

```xml
 <layout class="org.apache.log4j.PatternLayout">
     <param name="ConversionPattern" value="%d %-5p %-32t 
     [%X{SOFA-TraceId},%X{SOFA-SpanId}] - %m%n"/>
 </layout>
```
> 需要注意的是：`[%X{SOFA-TraceId},%X{SOFA-SpanId}]` 使我们推荐的打印格式，用户可以根据自己的实际需求场景进行定制

[附:基于 Logback 示例工程的源代码地址](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-slf4j)。


