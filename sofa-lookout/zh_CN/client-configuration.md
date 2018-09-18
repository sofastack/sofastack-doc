# 客户端配置

## 客户端配置项设置示例

```Java
lookoutConfig.setProperty(LookoutConfig.LOOKOUT_AGENT_HOST_ADDRESS,"127.0.0.1");

```
## 客户端配置项说明

| 配置项 | 对应 SpringBoot 配置项 | 默认配置值 | 说明 |
| ---| --- | ---| ---|
| lookout.enable | com.alipay.sofa.lookout.enable | true | 功能开关，默认是 true。如果改为 false，那么所有 metrics 就几乎没有内存与计算消耗(空对象与空方法) |
| lookout.max.metrics.num | com.alipay.sofa.lookout.max-metrics-num |5000 | metrics 最大数目限制，超过会自动忽略 |
| lookout.prometheus.exporter.server.port| com.alipay.sofa.lookout.prometheus-exporter-server-port | 9494 | prometheus 抓取的端口 |
| lookout.exporter.enable| com.alipay.sofa.lookout.exporter-enable | false | 是否开启支持被动采集的服务 |
| lookout.agent.host.address| com.alipay.sofa.lookout.agent-host-address | - | 主动上报 Agent 服务器的注解地址，支持多个地址以逗号分隔 |

## 客户端日志配置说明

| 系统属性配置项 | 对应 SpringBoot 配置项 | 默认配置值 | 说明 |
| ---| --- | ---| ---|
| -Dlogging.level.com.alipay.lookout=? | logging.level.com.alipay.lookout | warn | lookout 客户端的日志级别，debug 可以看见汇报数据的详情 |
| -Dlogging.path=? | logging.path | 当前用户目录 | SpringBoot V1的日志目录调整，包括 "lookout/" 日志子目录 |

## 客户端配置自定义(适用于 SpringBoot 技术栈模式)

使用配置定制扩展: MetricConfigCustomizerConfig
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

