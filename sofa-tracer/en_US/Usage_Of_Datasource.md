# Datasource Integration

In this document will demonstrate how to use SOFATracer to track of Datasource, this example(https://github.com/sofastack/sofa-tracer/tree/3.x/tracer-samples/tracer-sample-with-h2)。

Assuming you have built a simple Spring Web project based on SOFABoot, Then you can be operated by the following steps:

## Introduce SOFATracer
Introduce SOFATracer dependency in the new Spring Boot project:
```xml
<dependency>
	<groupId>com.alipay.sofa</groupId>
	<artifactId>tracer-sofa-boot-starter</artifactId>
	<version>2.2.0</version>
</dependency>
```

## Introduce h2database dependencies
For convenience, we use the h2database memory database for test. So, we need to introduce the following dependencies:
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

## Introduce the required connection pool dependencies
Introduce the required connection pool dependency packages, such as druid, c3p0, tomcat, dbcp, Hikari, and so on.
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

## Configure data source
Taking `HikariCP` as the example, we create a new  Spring configuration file named `datasource.xml`, which defines the followings:
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

## Application configuration
+ Required configuration

It should be noted that it is required to configure the application name when introducing SOFATracer; otherwise, the application may fails to start. This property is consistent with the SOFABoot framework requirements and is configured as follows:
```properties
spring.application.name=SOFATracerDataSource
```

+ Optional configuration

In order to run the sample project normally, you need to configure the h2database properties. Also for easy viewing of logs, configure the log path as follows:
```properties
# logging path
logging.path=./logs

# h2 web consloe path
spring.h2.console.path=/h2-console
#enable h2 web consloe, which defaults to false
spring.h2.console.enabled=true
#Allow remote access to h2 web consloe
spring.h2.console.settings.web-allow-others=true

spring.datasource.username=sofa
spring.datasource.password=123456
spring.datasource.url=jdbc:h2:~/test
spring.datasource.driver-class-name=org.h2.Driver
```

## Create a new Rest Service
In order to achieve the demo effect, we create a new Rest service and trigger SQL statement execution to view the tracer records of sql. The Rest service is created as follows:

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

You can see that the Rest service has created a table.

## Demonstration
Start the application and visit `localhost:8080/create` to execute the above Rest service. You can see the Tracer log of sql execution in `./logs/datasource-client-digest.log` and `./logs/datasource-client-stat.log`:
+ datasource-client-digest.log
```json
{"time":"2018-09-05 11:48:16.917","local.app":"SOFATracerDataSource","traceId":"1e323a031536119296795100182779","spanId":"0.1.2","database.name":"test","sql":"DROP TABLE IF EXISTS TEST;CREATE TABLE TEST(ID INT PRIMARY KEY%2C NAME VARCHAR(255));","result.code":"success","total.time":"103ms","connection.establish.span":"92ms","db.execute.cost":"8ms","database.type":"h2","database.endpoint":"jdbc:h2:~/test:-1","current.thread.name":"http-nio-8080-exec-1","baggage":""}
```

+ datasource-client-stat.log (print once in 60s by default)
```json
{"time":"2018-09-05 11:49:10.117","stat.key":{"local.app":"SOFATracerDataSource","database.name":"test"},"count":1,"total.cost.milliseconds":103,"success":"true","load.test":"F"}
```

## Other
Introduce the SOFATracer dependency in the Spring Boot project, which will automatically enable the DataSource events. You can disable starting DataSource by configuring the following switch, which is turned on by default:
```properties
com.alipay.sofa.tracer.datasource.enable=false
```

## Notes
+ To introduce SOFATracer, it is required to configure the application name; otherwise, the application fails to be started. The property name is: `spring.application.name`.
+ Based on the standard JDBC interface implementation, SOFATracer 2.2.0 theoretically supports event tracking for all standard database connection pools (such as DBCP, BoneCP, etc.). In Spring Boot environment, automatic event tracking is available for the five connection pools, namely DBCP, Druid, c3p0, tomcat, and HikariCP. It means that you only need to introduce SOFATracer dependency. In non-Spring Boot environment or for other connection pools (such as BoneCP), you need to configure them manually, for example:
```xml
<bean id="smartDataSource" class="com.alipay.sofa.tracer.plugins.datasource.SmartDataSource" init-method="init">
    <property name="delegate" ref="simpleDataSource"/>
    <!--application name-->
    <property name="appName" value="yourAppName"/>
    <!--database name -->
    <property name="database" value="yourDatabase"/>
    <!--database type, supporting MYSQL, ORACLE-->
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

That is, you need to configure the `com.alipay.sofa.tracer.plugins.datasource.SmartDataSource` provided by SOFATracer.