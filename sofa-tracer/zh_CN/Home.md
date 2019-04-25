# SOFATracer 介绍

SOFATracer 是蚂蚁金服开发的基于 [OpenTracing 规范](http://opentracing.io/documentation/pages/spec.html) 的分布式链路跟踪系统，其核心理念就是通过一个全局的 `TraceId` 将分布在各个服务节点上的同一次请求串联起来。通过统一的 `TraceId` 将调用链路中的各种网络调用情况以日志的方式记录下来同时也提供远程汇报到 [Zipkin](https://zipkin.io/) 进行展示的能力，以此达到透视化网络调用的目的。

## 功能描述

### 基于 OpenTracing 规范提供分布式链路跟踪解决方案

基于 [OpenTracing 规范](http://opentracing.io/documentation/pages/spec.html) 并扩展其能力提供链路跟踪的解决方案。各个框架或者组件可以基于此实现，通过在各个组件中埋点的方式来提供链路跟踪的能力。

### 提供异步落地磁盘的日志打印能力

基于 [Disruptor](https://github.com/LMAX-Exchange/disruptor) 高性能无锁循环队列，提供异步打印日志到本地磁盘的能力。框架或者组件能够在接入时，在异步日志打印的前提下可以自定义日志文件的输出格式。SOFATracer 提供两种类似的日志打印类型即摘要日志和统计日志，摘要日志：每一次调用均会落地磁盘的日志；统计日志：每隔一定时间间隔进行统计输出的日志。

### 支持日志自清除和滚动能力

异步落地磁盘的 SOFATracer 日志支持自清除和滚动能力，支持按照按照天清除和按照小时或者天滚动的能力

### 基于 SLF4J MDC 的扩展能力

SLF4J 提供了 MDC（Mapped Diagnostic Contexts）功能，可以支持用户定义和修改日志的输出格式以及内容。SOFATracer 集成了 SLF4J MDC 功能，方便用户在只简单修改日志配置文件即可输出当前 Tracer 上下文的 `TraceId` 和 `SpanId`。

### 界面展示能力

SOFATracer 可以将链路跟踪数据远程上报到开源产品 [Zipkin](https://zipkin.io/) 做分布式链路跟踪的展示。

### 统一配置能力

配置文件中提供丰富的配置能力以定制化应用的个性需求。

## 应用场景

解决在实施大规模微服务架构时的链路跟踪问题，达到透视化网络调用的目的，并可用于故障的快速发现，服务治理等。

## 组件埋点

目前 SOFATracer 支持 Spring MVC、标准 JDBC 接口实现的数据库连接池(DBCP、Druid、c3p0、tomcat、HikariCP、BoneCP)、HttpClient、Dubbo、Spring Cloud OpenFeign 等开源组件，其他开源组件（如 MQ、Redis）埋点支持在开发中。

支持组件 | 接入文档 | 支持版本
--------- | -------------  | -------------
Spring MVC | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_MVC) | 2.1.0
DBCP | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
Druid | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
c3p0 | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
HikariCP | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
HttpClient | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Datasource) | 2.2.0
RestTemplate | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_RestTemplate) | 2.3.0
OkHttp | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_OkHttp) | 2.3.2
Dubbo | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Dubbo) | 2.4.0
OpenFeign | [doc link](http://www.sofastack.tech/sofa-tracer/docs/Usage_Of_Dubbo) | 3.0.4
Redis | TODO | 
MQ | TODO | 

