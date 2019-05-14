# Development roadmap

<center>
![roadmap](https://gw.alipayobjects.com/mdn/rms_432828/afts/img/A*uprqRLDG2EMAAAAAAAAAAABjARQnAQ) 
</center>

## Task list

You are welcome to claim the task and [make contributions](https://github.com/sofastack/sofa-tracer/blob/master/CONTRIBUTING.md).

| Type | Task | Difficulty level | Claimant and time | Scheduled release time | Scheduled completion time | Progress | Related issue |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Code | SOFATracer performance optimization | High |  |  |  |  | [issue 18](https://github.com/sofastack/sofa-tracer/issues/18)and [issue 11]([https://github.com](https://github.com/)/alipay/sofa-tracer/issues/11) |
| Code | SOFATracer supports HttpClient | Medium |  |  |  | Completed, see [HttpClient access documentation](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_HttpClient) | [issue 17](https://github.com/sofastack/sofa-tracer/issues/17) |
| Code | SOFATracer supports running and configuring data reporting in non-SOFABoot framework | Medium |  |  |  | Completed,see [SOFATracer work in Spring Project](https://github.com/glmapper/tracer-zipkin-plugin-demo) | [issue 32](https://github.com/sofastack/sofa-tracer/issues/32) |
| Code | SOFATracer provides sampling capability | Medium |  |  |  | Completed,see [Use SOFATracer sampling capacity](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-sampler) | [issue 10](https://github.com/sofastack/sofa-tracer/issues/31) |
| Code | SOFATracer supports Zipkin 2.X | Medium |  |  |  | Completed,see [Remotely report data to Zipkin using SOFATrace](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-zipkin)r | [issue 23](https://github.com/sofastack/sofa-tracer/issues/23) |
| Code | SOFATracer supports Druid | Medium |  |  |  | Completed, see [DataSource access documentation](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |  |
| Code | SOFATracer supports c3p0 | Medium |  |  |  | Completed, see [DataSource access documentation](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |  |
| Code | SOFATracer supports Tomcat-JDBC | Medium |  |  |  | Completed, see [DataSource Access Documentation] (./Usage_Of_Datasource) |  |
| Code | SOFATracer supports HikariCP | Medium |  |  |  | Completed, see [DataSource Access Documentation] (./Usage_Of_Datasource) |  |
| Code | SOFATracer supports dbcp | Medium |  |  |  | Completed, see [DataSource access documentation](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |  |
| Code | SOFATracer supports dbcp2 | Medium |  |  |  | Completed, see [DataSource access documentation](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |  |
| Code | SOFATracer supports [Sharding-JDBC](https://github.com/sharding-sphere/sharding-sphere) | Medium |  |  |  |  |  |
| Code | SOFATracer supports Mysql JDBC Driver | Medium |  |  |  |  |  |
| Code | SOFATracer supports Oracle JDBC Driver | Medium |  |  |  |  |  |
| Code | SOFATracer Support Dubbo | Medium |  |  |  |  |  |
| Code | SOFATracer supports RestTemplate and AsyncRestTemplate | Medium |  |  |  | Completed,see [Recording RestTemplate link call data using SOFATracer](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-resttemplate) |  |
| Code | SOFATracer supports The standard servlet container  | Medium |  |  |  | Completed,see [Support for the standard servlet container (tomcat/jetty, etc.)](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-springmvc#%E5%AF%B9%E4%BA%8E%E6%A0%87%E5%87%86-servlet-%E5%AE%B9%E5%99%A8%E7%9A%84%E6%94%AF%E6%8C%81-tomcatjetty-%E7%AD%89) |  |
| Code | SOFATracer supports standalone link analysis, supports users to track events by annotation and supports reporting data to Zipkin for presentation | Medium |  |  |  |  |  |
| Code | SOFATracer supports Kafka | Medium |  |  |  |  |  |
| Code | SOFATracer supports Redis | Medium |  |  |  |  |  |
| Code | SOFATracer supports hystrix | Medium |  |  |  |  |  |
| Documentation | Document translation | Medium |  |  |  |  |  |


## Version iteration schedule

### 2.2.0 

* SOFATracer supports the common data source
    * SOFATracer supports Mysql Driver
    * SOFATracer supports [Sharding-JDBC](https://github.com/sharding-sphere/sharding-sphere)
    * SOFATracer supports Mysql-JDBC
    * SOFATracer supports Druid 
    * SOFATracer supports c3p0 
    * SOFATracer supports Tomcat-JDBC
    * SOFATracer supports HikariCP 
* SOFATracer supports HttpClient
* SOFATracer supports zipkin 2.X.X version

### 2.3.0

* SOFATracer supports RestTemplate & AsyncRestTemplate
* SOFATracer supports Sampler customization capability
* SOFATracer supports The standard servlet container
* SOFATracer supports Zipkin UI interface in Chinese
* SOFATracer supports Report date to Zipkin in Spring Project

### 2.4.0

* SOFATracer supports Dubbo
* SOFATracer supports Kafka
* SOFATracer Performance optimization

### 2.5.0

* SOFATracer supports stand-alone version link analysis ability
* SOFATracer supports manual reporting
* SOFATracer supports opentracing API buried point way 
* SOFATracer supports opentracing 0.30.x 
