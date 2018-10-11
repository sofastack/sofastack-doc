SOFATracer 2.2.0 基于标准的 JDBC 接口实现，支持对标准的数据库连接池（如 DBCP、Druid、c3p0、tomcat、HikariCP、BoneCP）埋点。下面演示如何接入 SOFATracer 埋点能力。

## 环境准备
使用 SOFATracer，需要先准备好基础环境，SOFATracer 依赖以下环境：

+ JDK7 或 JDK8
+ 需要采用 Apache Maven 3.2.5 或者以上的版本来编译

## 创建工程
SOFATracer 在 SOFABoot 和 Spring Boot 框架内均可使用。我们以 Spring Boot 为例演示，使用 [Spring Boot 的工程生成工具](https://start.spring.io/) 来生成。在该演示用例，我们需要添加一个 Web 的依赖同时编写一个简单的 REST 服务，以便最后在浏览器中查看效果。

## 引入 SOFATracer
在新建的 Spring Boot 工程引入 SOFATracer 依赖:
```xml
<dependency>
	<groupId>com.alipay.sofa</groupId>
	<artifactId>tracer-sofa-boot-starter</artifactId>
	<version>2.2.0</version>
</dependency>
```

## 引入 h2database 依赖
为了方便，我们使用 h2database 内存数据库测试，引入如下依赖：
```xml
<dependency>
	<groupId>com.h2database</groupId>
	<artifactId>h2</artifactId>
	<scope>runtime</scope>
</dependency>

<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
```

## 引入所需的连接池依赖
用户引入所需的连接池依赖包，如 druid, c3p0, tomcat, dbcp, Hikari 等。
```xml
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.0.12</version>
</dependency>
<dependency>
	<groupId>c3p0</groupId>
	<artifactId>c3p0</artifactId>
	<version>0.9.1.1</version>
</dependency>
<dependency>
	<groupId>org.apache.tomcat</groupId>
	<artifactId>tomcat-jdbc</artifactId>
	<version>8.5.31</version>
</dependency>
<dependency>
	<groupId>commons-dbcp</groupId>
	<artifactId>commons-dbcp</artifactId>
	<version>1.4</version>
</dependency>
<dependency>
	<groupId>com.zaxxer</groupId>
	<artifactId>HikariCP-java6</artifactId>
	<version>2.3.8</version>
</dependency>
```

## 配置数据源
我们以 `HikariCP` 为例，新建一个名为`datasource.xml` Spring 配置文件，定义如下内容:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- dataSource pool -->
    <bean id="simpleDataSource" class="com.zaxxer.hikari.HikariDataSource" destroy-method="close" primary="true">
        <property name="driverClassName" value="org.h2.Driver"/>
        <property name="jdbcUrl" value="jdbc:h2:~/test"/>
        <property name="username" value="sofa"/>
        <property name="password" value="123456"/>
    </bean>
</beans>
```

## 应用配置
+ 必要配置

需要注意一点，引入 SOFATracer 需要强制配置应用名，否则应用启动失败。这一属性和 SOFABoot 框架要求一致，配置如下：
```text
spring.application.name=SOFATracerDataSource
```

+ 非必要配置

为了该演示工程正常运行，需要配置 h2database 属性；另为了方便查看日志，配置日志路径。如下：
```text
# logging path
logging.path=./logs

# h2 web consloe 路径
spring.h2.console.path=/h2-console
# 开启 h2 web consloe，默认为 false
spring.h2.console.enabled=true
#允许远程访问 h2 web consloe
spring.h2.console.settings.web-allow-others=true

spring.datasource.username=sofa
spring.datasource.password=123456
spring.datasource.url=jdbc:h2:~/test
spring.datasource.driver-class-name=org.h2.Driver
```

## 新建 Rest 服务
为了达到演示效果，我们新建一个 Rest 服务，触发 SQL 语句执行，便于查看 sql 的 tracer 记录。Rest 服务如下创建：

```java
@RestController
public class SimpleRestController {

    @Autowired
    private DataSource simpleDataSource;

    @RequestMapping("/create")
    public Map<String, Object> create() {
        Map<String, Object> resultMap = new HashMap<String, Object>();
        try {
            Connection cn = simpleDataSource.getConnection();
            Statement st = cn.createStatement();
            st.execute("DROP TABLE IF EXISTS TEST;"
                    + "CREATE TABLE TEST(ID INT PRIMARY KEY, NAME VARCHAR(255));");
            resultMap.put("success", true);
            resultMap.put("result", "CREATE TABLE TEST(ID INT PRIMARY KEY, NAME VARCHAR(255))");
        } catch (Throwable throwable) {
            resultMap.put("success", false);
            resultMap.put("error", throwable.getMessage());
        }
        return resultMap;
    }

}
```

可以看到该 Rest 服务执行了一个建表操作。

## 演示
启动应用，访问 `localhost:8080/create` 执行上述 Rest 服务，可以在 `./logs/datasource-client-digest.log` 和 `./logs/datasource-client-stat.log` 看到 sql 执行的 tracer 日志：
+ datasource-client-digest.log
```json
{"time":"2018-09-05 11:48:16.917","local.app":"SOFATracerDataSource","traceId":"1e323a031536119296795100182779","spanId":"0.1.2","database.name":"test","sql":"DROP TABLE IF EXISTS TEST;CREATE TABLE TEST(ID INT PRIMARY KEY%2C NAME VARCHAR(255));","result.code":"success","total.time":"103ms","connection.establish.span":"92ms","db.execute.cost":"8ms","database.type":"h2","database.endpoint":"jdbc:h2:~/test:-1","current.thread.name":"http-nio-8080-exec-1","baggage":""}
```

+ datasource-client-stat.log (默认60s打印一次，)
```json
{"time":"2018-09-05 11:49:10.117","stat.key":{"local.app":"SOFATracerDataSource","database.name":"test"},"count":1,"total.cost.milliseconds":103,"success":"true","load.test":"F"}
```

## 其他
在 Spring Boot 工程引入 SOFATracer 依赖，会自动开启 DataSource 的埋点，可以通过设置如下开关禁止启动 DataSource 埋点，默认是打开的：
```text
com.alipay.sofa.tracer.datasource.enable=false
```

## 注意
+ 引入 SOFATracer 需要强制配置应用名，否则应用启动失败。属性名称为：`spring.application.name`
+ SOFATracer 2.2.0基于标准的 JDBC 接口实现，理论上支持对所有标准的数据库连接池（如 DBCP，BoneCP 等）埋点。在 Spring Boot 环境，对于 DBCP、Druid、c3p0、tomcat、HikariCP 五种连接池支持自动埋点，即用户只需要引入 SOFATracer 依赖即可。在非 Spring Boot 环境或者对其他连接池（如 BoneCP）还需要增加手动配置，比如：
```xml
<bean id="smartDataSource" class="com.alipay.sofa.tracer.plugins.datasource.SmartDataSource" init-method="init">
    <property name="delegate" ref="simpleDataSource"/>
    <!--应用名称 -->
    <property name="appName" value="yourAppName"/>
    <!--数据库名称 -->
    <property name="database" value="yourDatabase"/>
    <!--数据库类型，支持MYSQL, ORACLE-->
    <property name="dbType" value="MYSQL"/>
</bean>

<bean id="simpleDataSource" class="com.jolbox.bonecp.BoneCPDataSource" destroy-method="close">
    <property name="driverClass" value="com.mysql.jdbc.Driver" />
    <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1/yourdb" />
    <property name="username" value="root"/>
    <property name="password" value="abcdefgh"/>
    ...
</bean>
```
即需要额外配置 SOFATracer 提供的 `com.alipay.sofa.tracer.plugins.datasource.SmartDataSource`
