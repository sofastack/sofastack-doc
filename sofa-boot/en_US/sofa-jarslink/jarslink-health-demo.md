> [Project address](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/biz-health-check-sample)

## Introduction
SOFABoot extends the Health Check of Spring Boot. For detailed information, see [SOFABoot Documentation](../HealthCheck). This sample project is intended to demonstrate how to integrate the Health Check component of SOFABoot during merged deployment. Differences between the Health Check in merged deployment and that of a single SOFABoot application are as follows:
+ During static merged deployment, all Biz packages must pass the Health Check before the Ark package can be started normally.
+ When deploying the Biz packages dynamically in Jarslink2.0, all packages must pass the Health Check before successful deployment.
+ In merged deployment, a new check item named multiApplicationHealthChecker will be added when you access Spring Boot's default /health. The item is used to check the health of all Biz packages. Only after all Biz packages pass the Health Check can the merged package pass the Health Check.

## Dependency
To integrate the Health Check capability of SOFABoot in merged deployment, you need to add the following dependencies:
```xml
<!--health check-->
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
    <classifier>ark-plugin</classifier>
</dependency>

<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>

<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>healthcheck-sofa-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

**Note that spring-boot-starter-web is excluded to avoid starting multiple web applications when you introduce the healthcheck-sofa-boot-starter dependency.**

## Demo
+ cd biz-health-check-sample/app-one && mvn clean package
Execute the mvn clean package command in the app-one root directory and package the application into an Ark or Biz package. The file will be exported to the biz-health-check-sample/app-one/target directory.

+ cd biz-health-check-sample/app-two && mvn clean package
Execute the mvn clean package command in the app-two root directory and package the application into an Ark or Biz package. The file will be exported to the biz-health-check-sample/app-two/target directory.

+ Use java -jar to start the Ark package for app-one.

+ After the Ark package has started, visit http://localhost:8080/health in the browser. This is Spring Boot's default Health Check endpoint. A new check item named multiApplicationHealthChecker is added in the results and there is now only one Biz package. The page is displayed as follows:
```json
{"status":"UP","sofaBootComponentHealthCheckInfo":{"status":"UP","Middleware":{"RUNTIME-COMPONENT":{"status":"UP"}}},"springContextHealthCheckInfo":{"status":"UP"},"multiApplicationHealthChecker":{"status":"UP","Biz: app-one:1.0.0 health check":"passed"},"diskSpace":{"status":"UP","total":249769230336,"free":124531359744,"threshold":10485760}}
```

+ Run telnet localhost 1234 to access the Jarslink2.0 command interface and execute the install -b command to install the Biz package that starts app-two.

+ Visit http://localhost:8080/health again in the browser and you will find that the Health Check results for app-two are displayed in multiApplicationHealthChecker. The page is displayed as follows:
```json
{"status":"UP","sofaBootComponentHealthCheckInfo":{"status":"UP","Middleware":{"RUNTIME-COMPONENT":{"status":"UP"}}},"springContextHealthCheckInfo":{"status":"UP"},"multiApplicationHealthChecker":{"status":"UP","Biz: app-one:1.0.0 health check":"passed","Biz: app-two:1.0.0 health check":"passed"},"diskSpace":{"status":"UP","total":249769230336,"free":124521283584,"threshold":10485760}}
```
