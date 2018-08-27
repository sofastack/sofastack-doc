## 添加 Zipkin 数据汇报依赖

考虑到 Zipkin 的数据上报能力不是 SOFATracer 默认开启的能力，所以期望使用 SOFATracer 做数据上报时，需要添加如下的 Zipkin 数据汇报的依赖：

```xml
<dependency>
    <groupId>io.zipkin.java</groupId>
    <artifactId>zipkin</artifactId>
    <version>1.19.2</version>
</dependency>
<dependency>
    <groupId>io.zipkin.reporter</groupId>
    <artifactId>zipkin-reporter</artifactId>
    <version>0.6.12</version>
</dependency>
```
## 启动 Zipkin 服务端

启动 Zipkin 服务端用于接收 SOFATracer 汇报的链路数据，并做展示。Zipkin Server 的搭建可以[参考此文档](https://zipkin.io/)进行配置和服务端的搭建。

## SOFATracer 启用数据远程上报

在 SpringBoot 的配置文件 `application.properties` 中添加配置项：
1. 配置 `com.alipay.sofa.tracer.zipkin.enabled=true` 激活 SOFATracer 数据上报到 [Zipkin](https://zipkin.io/)。
2. 配置 Zipkin Server 端的地址 `com.alipay.sofa.tracer.zipkin.baseUrl=http://${ip}:${port}`。

配置好上述两个项目后，即激活了远程上报的能力，示例如下：

```
# enable Zipkin
com.alipay.sofa.tracer.zipkin.enabled=true
# Zipkin server url，假设 Zipkin server 端的服务地址：http://zipkin-cloud.host.net:9411
com.alipay.sofa.tracer.zipkin.baseUrl=http://zipkin-cloud.host.net:9411
```

附:[远程上报数据到 Zipkin 的源代码地址](https://github.com/alipay/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-zipkin)。前提：需要[参考此文档](https://zipkin.io/)对 Zipkin 服务端进行配置和搭建。



