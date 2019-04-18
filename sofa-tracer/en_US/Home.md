# Introduction to SOFATracer

SOFATracer is a distributed link tracing system based on [OpenTracing specification](http://opentracing.io/documentation/pages/spec.html) developed by Ant Financial. Its core concept is to concatenate the same request distributed on each service node with a global `TraceId`. By the unified `TraceId`, it can record the various network call information in the call link in logs, and can remotely report the call records to [Zipkin](https://zipkin.io/) for presentation, thus implementing perspective network call.

## Features

### Distributed link tracing solution based on OpenTracing specification

SOFATracer is a solution that provides link tracing based on and improved from the [OpenTracing specification](http://opentracing.io/documentation/pages/spec.html). Based on this implementation, each framework or component can provide the ability to link tracking by burying points.

### Provide asynchronous log printing to disks

Based on high-performance lock-free loop queue of [Disruptor](https://github.com/LMAX-Exchange/disruptor), SOFATracer provides the ability to print logs asynchronously to local disk. The introduced framework or component can customize the output format of the log file under the premise of asynchronous log printing. SOFATracer provides two types of logs, digest log and statistical log. Digest log: logs that are printed to disk upon each call. Statistical log: logs that are printed at regular intervals.

### Support automatic log cleanup and scrolling

Asynchronous SOFATracer log supports automatic cleanup and scrolling, and supports cleaning by day and scrolling by hour or day.

### Extended based on SLF4J MDC

SLF4J provides MDC (Mapped Diagnostic Contexts), which supports user to define and modify the output log format and content. SOFATracer integrates the SLF4J MDC function, which allows user to output the `TraceId` and `SpanId` of the current Tracer context by simply modifying the log configuration file.

### Interface presentation

SOFATracer can remotely report link tracing data to the open-source product [Zipkin](https://zipkin.io/) for distributed link tracing presentation.

### Unified configuration

The profile file provides various configuration options for you to customize the individual requirements of the application.

## Scenario

SOFATracer solves the problem of link tracing when implementing large-scale microservice architecture, achieves perspective network call, and can be used to rapidly Failures Discovery, Service Governance, and so on.

## Component event tracking
At present, SOFATracer supports Spring MVC, database connection pool (DBCP, Druid, c3p0, tomcat, HikariCP, BoneCP) acheived by standard JDBC interface, HttpClient and other open-source components. Event tracking for other open-source components (such as MQ, Redis) is still in development.

Component | Document| Version
--------- | ------------- | -------------
Spring MVC | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_MVC) | 2.1.0
DBCP | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
Druid | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
C3p0 | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
HikariCP | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
HttpClient | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
RestTemplate | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_RestTemplate) | 2.3.0
OkHttp | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_OkHttp) | 2.3.2
Dubbo | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Dubbo) | 2.4.0
OpenFeign | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Dubbo) | 3.0.4
Redis | TODO | 
MQ | TODO | 
