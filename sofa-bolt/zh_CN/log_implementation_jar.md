## 日志依赖参考
门面我们是用SLF4，业务可根据自己的实际需要，选择某一种日志实现

```
<!-- 日志门面 (仅支持slf4j) -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.5</version>
</dependency>

<!-- 日志实现从如下3个里面选择一种
<!-- 日志实现1: log4j1.x START-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.5</version>
</dependency>
<!-- 注意: 在主pom、ace pom及test pom均要增加 toolkit-common-logging 依赖 -->
<dependency>
  <groupId>com.alibaba.toolkit.common</groupId>
  <artifactId>toolkit-common-logging</artifactId>
  <version>1.11</version>
</dependency>
<!-- 日志实现1: log4j1.x END-->

<!-- 日志实现2: log4j2.x START-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.8</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.8</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.8</version>
</dependency>
<dependency>
  <groupId>com.lmax</groupId>
  <artifactId>disruptor</artifactId>
  <version>3.2.0</version>
</dependency>
<!-- 日志实现2: log4j2.x END-->

<!-- 日志实现3: logback START-->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.3</version>
</dependency>
<!-- 日志实现3: logback END-->
```