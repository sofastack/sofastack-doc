# 发展路线

<center>
![roadmap](https://gw.alipayobjects.com/mdn/rms_432828/afts/img/A*3E6rQ6fE9RkAAAAAAAAAAABjARQnAQ) 
</center>

## 任务列表

欢迎大家领取任务[参与贡献](https://github.com/sofastack/sofa-tracer/blob/master/CONTRIBUTING.md)。

| 类型 | 任务| 困难度 | 认领人及时间 | 计划发布时间 | 计划完成时间 | 进度| 相关 issue|
| ----|--  | ------| ------------| ------------| ------------ | ----| -------- |
| 代码 | SOFATracer 性能优化专题                                      | 高     |              |              |              |                                                              | [issue 18](https://github.com/sofastack/sofa-tracer/issues/18)和 [issue 11](https://github.com/sofastack/sofa-tracer/issues/11) |
| 代码 | SOFATracer 支持 HttpClient                                   | 中     |              |              |              | 已经完成，见 [HttpClient 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_HttpClient) | [issue 17](https://github.com/sofastack/sofa-tracer/issues/17)  |
| 代码 | SOFATracer 数据汇报能力提供在非 SOFABoot 框架下的运行和配置能力 | 中     |              |              |              | 已经完成，见 [Spring 工程中使用 SOFATracer](https://github.com/glmapper/tracer-zipkin-plugin-demo) | [issue 32](https://github.com/sofastack/sofa-tracer/issues/32)  |
| 代码 | SOFATracer 提供采样能力                                      | 中     |              |              |              | 已经完成，见[使用 SOFATracer 的采样能力](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-sampler) | [issue 10](https://github.com/sofastack/sofa-tracer/issues/31)  |
| 代码 | SOFATracer 支持 Zipkin 2.X.X 版本                            | 中     |              |              |              | 已经完成，见[使用 SOFATracer 远程汇报数据到 Zipkin](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-zipkin) | [issue 23](https://github.com/sofastack/sofa-tracer/issues/23)  |
| 代码 | SOFATracer 支持 Druid                                        | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 c3p0                                         | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 Tomcat-JDBC                                  | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 HikariCP                                     | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 dbcp                                         | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 dbcp2                                        | 中     |              |              |              | 已经完成，见 [DataSource 接入文档](https://yuque.antfin-inc.com/guolei.sgl/ktyzyg/vrsw5h/Usage_Of_Datasource) |                                                              |
| 代码 | SOFATracer 支持 [Sharding-JDBC](https://github.com/sharding-sphere/sharding-sphere) | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 Mysql JDBC Driver                            | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 Oracle JDBC Driver                           | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 Dubbo                                        | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 RestTemplate 和 AsyncRestTemplate            | 中     |              |              |              | 已经完成，见[使用 SOFATracer 记录 RestTemplate 链路调用数据](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-resttemplate) |                                                              |
| 代码 | SOFATracer 支持标准Servlet                                   | 中     |              |              |              | 已经完成，见[对于标准 servlet 容器的支持（ tomcat/jetty 等）](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-springmvc#%E5%AF%B9%E4%BA%8E%E6%A0%87%E5%87%86-servlet-%E5%AE%B9%E5%99%A8%E7%9A%84%E6%94%AF%E6%8C%81-tomcatjetty-%E7%AD%89) |                                                              |
| 代码 | SOFATracer 支持单机版链路分析并给用户通过注解使用的埋点方式，数据汇报到 Zipkin | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 Kafka                                        | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 Redis                                        | 中     |              |              |              |                                                              |                                                              |
| 代码 | SOFATracer 支持 hystrix                                      | 中     |              |              |              |                                                              |                                                              |
| 文档 | 文档翻译                                                     | 中     |              |              |              |                                                              |                                                              |

## 版本迭代计划

### 2.2.0 

* SOFATracer 支持 JDBC 数据源
    * SOFATracer 支持 Mysql Driver
    * SOFATracer 支持 [Sharding-JDBC](https://github.com/sharding-sphere/sharding-sphere)
    * SOFATracer 支持  Mysql-JDBC
    * SOFATracer 支持  Druid 
    * SOFATracer 支持  c3p0 
    * SOFATracer 支持  Tomcat-JDBC
    * SOFATracer 支持  HikariCP 
* SOFATracer 支持 HttpClient
* SOFATracer 支持 Zipkin 2.X.X 版本，开发验证并测试

### 2.3.0

* SOFATracer 支持RestTemplate 和 AsyncRestTemplate
* SOFATracer 支持提供采样能力
* SOFATracer 支持标准 servlet 容器
* SOFATracer 支持 Zipkin UI 中文界面
* SOFATracer 支持数据汇报能力提供在非 SOFABoot 框架下的运行和配置能力

### 2.4.0

* SOFATracer 支持 Dubbo
* SOFATracer 支持 Kafka
* SOFATracer 性能优化专题

### 2.5.0

* SOFATracer 支持单机版链路分析并给用户通过注解使用的埋点方式，数据汇报到 Zipkin 展示
* SOFATracer 支持 manual report
* SOFATracer 支持 基于 Opentracing API 埋点方式
* SOFATracer 支持 Opentracing 0.30.0+ 版本


