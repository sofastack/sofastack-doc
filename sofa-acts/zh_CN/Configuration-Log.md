# 预跑返填日志配置

## logback 日志工具

```xml
<appender name="ACTS" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <append>true</append>
    <!-- a filter that show green light for object that has a error log level-->
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>debug</level>
    </filter>
    <!-- log name -->
    <file>${logging.path}/acts-sql.log</file>
    <!-- to generate a log file everyday with a longest lasting of 30 days -->
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
        <!-- logfile name with daily rolling-->
        <FileNamePattern>${logging.path}/acts-sql.log.%d{yyyy-MM-dd}
        </FileNamePattern>
        <!-- log perserve days-->
        <MaxHistory>7</MaxHistory>
    </rollingPolicy>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
        <!--output format：%d is for date，%thread is for thread name，%-5level：loglevel with 5 character  %msg：log message，%n line breaker-->
        <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        <!-- encoding -->
        <charset>UTF-8</charset>
    </encoder>
</appender>

<!--以 iBatis 日志为例-->
<logger name="com.ibatis" level="DEBUG">
    <appender-ref ref="ACTS"/>
</logger>

<logger name="java.sql" level="debug">
    <appender-ref ref="ACTS"/>
</logger>

<!-- ACTS 日志标记-->
<logger name="acts-sql-logger" level="debug"
    <appender-ref ref="ACTS"/>
</logger>
```

## log4j 日志工具

```plain
log4j.logger.com.ibatis=debug,ACTS
log4j.logger.java.sql=debug,ACTS
log4j.logger.acts-sql-logger=INFO,ACTS
log4j.appender.ACTS=org.apache.log4j.RollingFileAppender
log4j.appender.ACTS.File=logs/acts-sql.log
log4j.appender.ACTS.layout=org.apache.log4j.PatternLayout
log4j.appender.ACTS.layout.ConversionPattern=%d{HH:mm:ss,SSS} [%t] %-5p %C{1} : %m%n
```
