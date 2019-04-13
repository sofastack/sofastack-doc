# SOFATracer configuration item

After introducing SOFATracer, you can add related configuration items in Spring Boot configuration file `application.properties` to customize the behaviors of SOFATracer.

For SOFATracer log output directory, you can configure `logging.path` in `application.properties`, then the log output path is `${logging.path}/tracelog`; if `logging.path` is not configured, the default output path is `${user.home}/logs/tracelog`.

Configuration item | Description | Default value
----|------|----
logging.path | log output directory | SOFATracer output logs to `logging.path` directory in priority; If the directory is not configured, log will be output to `${user.home}` by default.
com.alipay.sofa.tracer.disableDigestLog | Disable all integrated SOFATracer summary log printing | false
com.alipay.sofa.tracer.disableConfiguration[${logType}] | Disable specific SOFATracer summary log printing of `${logType}`. `${logType}` indicates the log type, such as spring-mvc-digest.log | false
com.alipay.sofa.tracer.tracerGlobalRollingPolicy | SOFATracer log rolling policy | yyyy-MM-dd：roll by day；<br/>yyyy-MM-dd_HH：roll by hour;<br/>Logs are not rolled by day by default.
com.alipay.sofa.tracer.tracerGlobalLogReserveDay | Retention days of SOFATracer logs | Retained for 7 days by default.
com.alipay.sofa.tracer.statLogInterval | Time interval of statistical logs, unit: second |Output statistical logs once every 60 seconds by default
com.alipay.sofa.tracer.baggageMaxLength | Maximum length for retaining penetration data | Default: 1024
| com.alipay.sofa.tracer.zipkin.enabled | Whether to enable SOFATracer remote data reporting to Zipkin | true: enable; false: disable. Disabled by default.
|com.alipay.sofa.tracer.zipkin.baseUrl| The address Zipkin address to which SOFATracer remotely reports data, which works only in the case of `com.alipay.sofa.tracer.zipkin.enabled=true` | Format: `http: //${host}:${port}`
| com.alipay.sofa.tracer.springmvc.filterOrder | Order validated by SOFATrace Filter intergrated in SpringMVC | -2147483647(`org.springframework.core.Ordered#HIGHEST_PRECEDENCE + 1`)
| com.alipay.sofa.tracer.springmvc.urlPatterns | URL Pattern paths validated by SOFATrace Filter intergrated in SpringMVC  | `/*`: All validated
