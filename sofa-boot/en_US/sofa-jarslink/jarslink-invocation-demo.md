> [Project address](https://github.com/sofastack/sofa-jarslink/tree/master/sofa-jarslink-samples/biz-jvm-invocation-sample)

## Introduction
During merged deployment, Biz packages can communicate with each other by releasing and referencing JVM services apart from using the RPC framework. This sample project is intended to demonstrate how two Biz packages communicate by JVM services.

Within the biz-jvm-invocation-sample project, there are three sub-projects whose functions are as follows:
+ facade: A common Java module that defines the SampleJvmService interface.
```java
package me.qlong.tech.service;

public interface SampleJvmService {
    String service();
}
```
+ app-one: A SOFABoot Web application that defines a simple rest request and use the `@SofaReference` annotation to reference the SampleJvmService. When a page request is triggered, an attempt is made to call the JVM service. The key code is:
```java
package me.qlong.controller;

import com.alipay.sofa.runtime.api.annotation.SofaReference;
import me.qlong.tech.service.SampleJvmService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @SofaReference
    private SampleJvmService sampleJvmService;

    @RequestMapping("/hello")
    public String hello() {
        return sampleJvmService.service();
    }

}
```

+ app-two: A non-web application in SOFABoot that uses the `@SofaService` annotation to publish the SampleJvmService. 
```java
package me.qlong.tech.service.impl;

import com.alipay.sofa.runtime.api.annotation.SofaService;
import me.qlong.tech.service.SampleJvmService;
import org.springframework.stereotype.Component;

@SofaService
@Component
public class AppTwoSampleService implements SampleJvmService{
    public String service() {
        return "App Two";
    }
}
```

## Dependency
To communicate between Biz packages through JVM services, you must add dependencies on the SOFARuntime package and the corresponding Ark Plugin as follows:
```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
    <classifier>ark-plugin</classifier>
</dependency>
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>runtime-sofa-boot-starter</artifactId>
</dependency>
```
For detailed information about publishing and referencing JVM services, see the [SOFABoot Documentation](../Module-Service). You are advised to use annotations in Jarslink2.0.

## Demo
+ cd biz-jvm-invocation-sample/facade && mvn clean install 
Execute the mvn clean install command in the facade root directory, and install the facade package in the local Maven repository so that you can add a facade dependency in app-one and app-two:
```xml
<!--service facade-->
<dependency>
    <groupId>me.qlong.tech</groupId>
    <artifactId>facade</artifactId>
    <version>1.0.0</version>
</dependency>
```

+ cd biz-jvm-invocation-sample/app-one && mvn clean package
Execute the mvn clean package command in the app-one root directory and package the application into Ark and Biz packages. The files will be exported to the biz-jvm-invocation-sample/app-one/target directory.

+ cd biz-jvm-invocation-sample/app-two && mvn clean package
Execute the mvn clean package command in the app-two root directory and package the application into Ark and Biz packages. The files will be exported to the biz-jvm-invocation-sample/app-two/target directory.

+ Use java -jar to start the Ark package for app-one.
+ After the Ark package has started, visit http://localhost:8080/hello in the browser. The following error message is displayed on the page warning that the JVM service for me.qlong.tech.service.SampleJvmService cannot be found:
```text
There was an unexpected error (type=Internal Server Error, status=500).
JVM Reference[interface me.qlong.tech.service.SampleJvmService#] cant not find the corresponding JVM service. Please check if there is a SOFA deployment publish the corresponding JVM service. If this exception occurred when the application starts up, please add Require-Module to SOFA deployment's MANIFEST.MF to indicate the startup dependency of SOFA modules.
```
+ The error message appears because handling the /hello request triggers app-one to call the me.qlong.tech.service.SampleJvmService and no other Biz packages are publishing the service at this moment.
+ Run telnet localhost 1234 to access the Jarslink2.0 command interface and execute the install -b command to install the Biz package that starts app-two.
+ Visit http://localhost:8080/hello again in the browser and the page displays "App Two.” This indicates that the request successfully triggered app-one to call the me.qlong.tech.service.SampleJvmService published by app-two.
