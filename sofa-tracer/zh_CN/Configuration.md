# SOFATracer 配置项

应用在引入 SOFATracer 后，可以在 Spring Boot 的配置文件 `application.properties` 中添加相关配置项来定制 SOFATracer 的相关行为。

SOFATracer 的日志输出目录，可以在 `application.properties` 中配置 `logging.path` 的路径，那么其日志输出路径为 `${logging.path}/tracelog`；如果没有配置 `logging.path`，那么 SOFATracer 的默认输出路径为 `${user.home}/logs/tracelog`。

SOFATracer 配置项 | 说明 | 默认值
----|------|----
logging.path | 日志输出目录  | SOFATracer 会优先输出到 `logging.path` 目录下；如果没有配置日志输出目录，那默认输出到 `${user.home}`
com.alipay.sofa.tracer.disableDigestLog | 是否关闭所有集成 SOFATracer 组件摘要日志打印  | false
com.alipay.sofa.tracer.disableConfiguration[${logType}] | 关闭指定 `${logType}` 的 SOFATracer 组件摘要日志打印。`${logType} `是指具体的日志类型，如：`spring-mvc-digest.log`  | false
| com.alipay.sofa.tracer.tracerGlobalRollingPolicy | SOFATracer 日志的滚动策略 | `.yyyy-MM-dd`：按照天滚动；`.yyyy-MM-dd_HH`：按照小时滚动。默认不配置按照天滚动
| com.alipay.sofa.tracer.tracerGlobalLogReserveDay | SOFATracer 日志的保留天数 | 默认保留 `7` 天
| com.alipay.sofa.tracer.statLogInterval | 统计日志的时间间隔，单位：秒 | 默认 `60` 秒统计日志输出一次
| com.alipay.sofa.tracer.baggageMaxLength | 透传数据能够允许存放的最大长度 | 默认值 `1024`
| com.alipay.sofa.tracer.zipkin.enabled | 是否开启 SOFATracer 远程上报数据到 Zipkin | true：开启上报；false：关闭上报。默认不上报
|com.alipay.sofa.tracer.zipkin.baseUrl| SOFATracer 远程上报数据到 Zipkin 的地址，`com.alipay.sofa.tracer.zipkin.enabled=true`时配置此地址才有意义 | 格式：`http://${host}:${port}`
| com.alipay.sofa.tracer.springmvc.filterOrder | SOFATracer 集成在 SpringMVC 的 Filter 生效的 Order  | -2147483647（`org.springframework.core.Ordered#HIGHEST_PRECEDENCE + 1`）
| com.alipay.sofa.tracer.springmvc.urlPatterns | SOFATracer 集成在 SpringMVC 的 Filter 生效的 URL Pattern 路径 | `/*` 全部生效

