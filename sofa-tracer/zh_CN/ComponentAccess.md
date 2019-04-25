# 快速开始

SOFATracer 接入的组件列表参考：[SOFATracer 介绍](https://www.sofastack.tech/sofa-tracer/docs/Home),在使用时请注意不同组件对应的SOFATracer 版本和 JDK 版本。

### 环境准备

要使用 SOFABoot，需要先准备好基础环境，SOFABoot 依赖以下环境：
- JDK7 或 JDK8 
- 需要采用 Apache Maven 3.2.5 或者以上的版本来编译

### 示例列表

下面所有 Samples 工程均为 SOFABoot 工程(同时支持 SpringBoot 工程中使用)，关于如何创建 SOFABoot 工程请参考 [SOFABoot 快速开始](https://www.sofastack.tech/sofa-boot/docs/QuickStart)。

* 组件接入
    * [Spring MVC 埋点接入](./Usage_Of_MVC)
    * [HttpClient 埋点接入](./Usage_Of_HttpClient)
    * [DataSource 埋点接入](./Usage_Of_Datasource)
    * [RestTemplate 埋点接入](./Usage_Of_RestTemplate)
    * [OkHttp 埋点接入](./Usage_Of_OkHttp)
    * [SOFARPC 埋点接入](https://www.sofastack.tech/sofa-rpc/docs/Invoke-Chain-Pass-Data)
    * [Dubbo 埋点接入](./Usage_Of_Dubbo)
    * [Spring Cloud OpenFeign 埋点接入](./Usage_Of_openfeign)
* [采样](./Sampler)
* [上报数据到 Zipkin](./ReportToZipkin)