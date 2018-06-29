# 使用手册

## Registry 的使用

不同的 Registry 的集成提供了不同的访问 Metrics 的方式。

### 1. 对接到 Prometheus

SOFALookout 的数据可以对接到 [Prometheus](https://prometheus.io/) 上面。为了将数据对接到 Prometheus 上面，首先需要在工程中加入依赖：

```xml
<dependency>
     <groupId>com.alipay.sofa.lookout</groupId>
     <artifactId>lookout-sofa-boot-starter</artifactId>
</dependency>
<dependency>
     <groupId>com.alipay.sofa.lookout</groupId>
     <artifactId>lookout-reg-prometheus</artifactId>
     <version>${lookout.client.version}</version>
</dependency>
```

添加好依赖之后，启动应用，既可以访问 http://localhost:9494 来看到数据，其中 9494 为默认的端口，可以通过在 `application.properties` 里面配置 `com.alipay.sofa.lookout.prometheus-exporter-server-port` 来改变端口。

有了访问数据的 URL 之后，可以编辑一个 `prometheus.yml` 来抓取该项目信息，假设本机 IP 地址为 10.15.232.20，那么可以配置如下的 `prometheus.yml`：

```yaml
scrape_configs:
  - job_name:       'lookout-client'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.15.232.20:9494']
```

有了上面的配置文件之后，可以再到本地通过 Docker 来启动 Prometheus：

```
docker run -d -p 9090:9090 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml  --name prom prom/prometheus:master
```

然后通过浏览器访问: http://localhost:9090，再通过 PromQL 查询即可查询到对应的 Metrics。

SOFALookout 中也提供了一个[对接 Prometheus 的样例](https://github.com/alipay/sofa-lookout/tree/master/client/samples/lookout-client-samples-prometheus)，大家可以前往自行查看。

### 2. 对接 Dropwizard

除了 Prometheus 之外，SOFALookout 的数据也可以注解注册到 [Dropwizard Metrics](https://metrics.dropwizard.io/4.0.0/) 上。

通过把数据注册到 Dropwizard Metrics 上，可以与 SpringBoot 1.x 的 Actuator 的相集成，为了和 Dropwizard Metrics 做集成，需要添加如下的依赖：

```xml
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

然后启动后访问 http://localhost:8080/metrics 既可以看到通过 SOFALookout 的数据。

SOFALookout 也提供了[和 Dropwizard Metrics 的集成的样例工程](https://github.com/alipay/sofa-lookout/tree/master/client/samples/lookout-client-samples-boot)，大家可以前往自行查看。