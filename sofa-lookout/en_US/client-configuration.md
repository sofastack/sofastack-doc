# Client configuration

## Client configuration example

```Java
lookoutConfig.setProperty(LookoutConfig.LOOKOUT_AGENT_HOST_ADDRESS,"127.0.0.1");

```
## Description of server configuration item 

| Configuration item | Corresponding SpringBoot configuration item | Default value | Description |
| ---| --- | ---| ---|
| lookout.enable | com.alipay.sofa.lookout.enable | true | Function switch, it defaults to true. If you change it to false (empty objects and empty methods), then all metrics comsume almost no memory and computing resource  |
| lookout.max.metrics.num | com.alipay.sofa.lookout.max-metrics-num |5000 | Maximum number limit of metrics, over which will be automatically ignored |
| lookout.prometheus.exporter.server.port| com.alipay.sofa.lookout.prometheus-exporter-server-port | 9494 | The port got by Prometheus |
Lookout.exporter.enable| com.alipay.sofa.lookout.exporter-enable | false | Whether or not to enable services that support passive collection |
Lookout.agent.host.address| com.alipay.sofa.lookout.agent-host-address | - | Proactively report the annotation address of the Agent server. Multiple addresses are separated by commas |

## Description of client log configuration

| Configuration item of system property  | Corresponding SpringBoot configuration item | Default Value | Description |
| ---| --- | ---| ---|
| -Dlogging.level.com.alipay.lookout=? | logging.level.com.alipay.lookout | warn | The log level of Lookout client. Debug to see the details of the report data |
| -Dlogging.path=? | logging.path | Directory of the current user | Modify SpringBoot V1 log directory, including "lookout/" log subdirectory |

## Custom client configuration (suitable for SpringBoot technology stack mode)

Use configuration custom extensions: MetricConfigCustomizerConfig
```
@Configuration
public class MetricConfigCustomizerConfig {

    @Bean
    public MetricConfigCustomizer metricConfigCustomizer() {
        return new MetricConfigCustomizer() {
            @Override
            public void customize(MetricConfig metricConfig) {
                metricConfig.addProperty("testaa", "testbb");
            }
        };
    }
}
```