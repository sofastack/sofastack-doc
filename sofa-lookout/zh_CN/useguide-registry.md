# 使用手册
## Registry 的使用

不同的 Registry 提供了不同的访问 Metrics 的方式。

### 1.lookout-reg-prometheus

提供了与 [Prometheus](https://prometheus.io/) 集成的能力。详细可以参考样例工程：

#### （1）加入依赖

```xml
<dependency>
     <groupId>com.alipay.sofa.lookout</groupId>
     <artifactId>lookout-sofa-boot-starter</artifactId>
     <version>${lookout.client.version}</version>
</dependency>
<dependency>
     <groupId>com.alipay.sofa.lookout</groupId>
     <artifactId>lookout-reg-prometheus</artifactId>
     <version>${lookout.client.version}</version>
</dependency>
```
#### （2）启动samples, 后可以访问：`http://localhost:${com.alipay.sofa.lookout.prometheus-exporter-server-port}`

默认端口是：9494， 但是可以通过上面占位符对应的配置项进行修改。

#### （3）可以配合 prometheus 服务查看

- prometheus.yml 编辑可以抓取该项目信息, 假设本机IP：10.15.232.20

```
scrape_configs:
  - job_name:       'lookout-client'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.15.232.20:9494']

```
- 本地运行 prometheus docker

```
docker run -d -p 9090:9090 -v prometheus.yml:/etc/prometheus/prometheus.yml  --name prom prom/prometheus:master
```

- 浏览器访问: http://localhost:9090 ,通过 PromQL 查询并聚合相应的 Metrics 吧。



### 2.lookout-reg-dropwizard

可以将通过 Lookout Client 采集的 metrics 数据适配注册在 [Dropwizard Metrics](https://metrics.dropwizard.io/4.0.0/) 上。

通过该注册表模块，结合 Dropwizard Metrics的依赖，可以与 SpringBoot V1 的 Actuator 的相集成，更多细节可参考样例工程: lookout-client-samples-boot。

- 辅助依赖

```
<dependency>
     <groupId>com.alipay.sofa.lookout</groupId>
     <artifactId>lookout-reg-dropwizard</artifactId>
     <version>${lookout.client.version}</version>
</dependency>
<dependency>
     <groupId>io.dropwizard.metrics</groupId>
     <artifactId>metrics-core</artifactId>
</dependency>
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

- 启动后访问：http://localhost:8080/metrics

### 3.lookout-reg-server

默认依赖的，该模块作用是向 Lookout Server 进行 metrics 数据上报；更多细节（TODO）