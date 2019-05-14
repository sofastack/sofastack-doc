# User guide

## Use Registry
Different Registry integrations provide different ways to access Metrics.

### 1. LookoutRegistry

Provides the ability to count metrics by a time window. It is divided into two modes: “active” and “passive”. The passive mode is off currently.

(1) Active mode

     You can specify the IP address of the remote agent through [Client Configuration], that is, check when start reporting, and regularly report data.

(2) Passive mode

     This mode can be activated through [Client Configuration], and HTTP service is provided on port 19399.


### 2. Connect to Prometheus

The data of SOFALookout can be shared with [Prometheus](https://prometheus.io/). In order to connect to Prometheus, you first need to add dependencies to your project:

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

After adding the dependencies, launch the application, and you can see the data by visiting http://localhost:9494, where 9494 is the default port, you can configure `com.alipay.sofa.lookout.prometheus-exporter-server-port` in `application.properties` to change the port.

Once you have the URL to access the data, you can edit a `prometheus.yml` to grab the project information. Assuming that the local IP address is 10.15.232.20, you can configure `prometheus.yml` as follows:

```yaml
scrape_configs:
  - job_name:       'lookout-client'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.15.232.20:9494']
```

With the above configuration file, you can start Prometheus locally via Docker:

```
docker run -d -p 9090:9090 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml  --name prom prom/prometheus:master
```

Then visit http://localhost:9090 through the browser, and you can query the corresponding Metrics through PromQL.

[An example of connecting to Prometheus](https://github.com/sofastack/sofa-lookout/tree/master/client/samples/lookout-client-samples-prometheus) is also available in SOFALookout, so you can go and see it as a reference.

### 3. Connect to SpringBoot actuator

In addition to Prometheus, SOFALookout can be integrated with the Actuator of SpringBoot 1.x by adding the following dependency:

```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Then, start and visit http://localhost:8080/metrics to see the data of events logged by the SOFALookout API.

SOFALookout also provides [an integrated sample project](https://github.com/sofastack/sofa-lookout/tree/master/client/samples/lookout-client-samples-boot).