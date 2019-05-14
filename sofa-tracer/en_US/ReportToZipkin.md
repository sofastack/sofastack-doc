# Remotely report data to Zipkin using SOFATracer

This example demonstrates how to remotely report link data to [Zipkin] (https://zipkin.io/) by configuring SOFATracer in an application that integrates SOFATracer.

The following examples demonstrate how to use them in SOFABoot/SpringBoot projects and non-SOFABoot/SpringBoot projects, respectively.

## Prepare environment
To use SOFABoot, you need to prepare the basic environment first. SOFABoot relies on the following environments:

+ JDK7 or JDK8
+ Apache Maven 3.2.5+ required for compilation

## Introduce SOFABoot

After creating a Spring Boot project, you need to introduce the SOFABoot dependency. First, you need to unzip the `zip` package of the Spring Boot project generated above and modify the Maven project configuration file `pom.xml`.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>${spring.boot.version}</version>
    <relativePath/>
</parent>
```

Replace the above with the followings:

```xml
<parent>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>sofaboot-dependencies</artifactId>
    <version>${sofa.boot.version}</version>
</parent>
```
The `${sofa.boot.version}` specifies the latest version of SOFABoot. For more about SOFABoot versions, see [Release notes](https://github.com/sofastack/sofa-boot/releases). 

## Add SOFATracer starter

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>tracer-sofa-boot-starter</artifactId>
</dependency>
```

## Application configuration

Finally, add the properties to be used by SOFATracer under the project's `application.properties` file, including `spring.application.name` to indicate the name of the current application; `logging.path` to specify the output directory of the log.

```properties
# Application Name
spring.application.name=SOFATracerReportZipkin
# logging path
logging.path=./logs
# open zipkin report
com.alipay.sofa.tracer.zipkin.enabled=true
# specify zipkin server address
com.alipay.sofa.tracer.zipkin.baseUrl=http://localhost:9411
```

## Configure Zipkin Dependencies

Considering that Zipkin's data reporting capability is not the ability of SOFATracer to be enabled by default,it‘s desirable to add the following Zipkin data reporting dependencies when using SOFATracer for data reporting:
```xml
<dependency>
    <groupId>io.zipkin.zipkin2</groupId>
    <artifactId>zipkin</artifactId>
    <version>2.11.12</version>
</dependency>
<dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter</artifactId>
    <version>2.7.13</version>
</dependency>	
```	
## Start the Zipkin server

Start the Zipkin server to receive the link data reported by SOFATracer and display it. Zipkin Server can be configured with reference to [this document](https://zipkin.io/).

## Running

You can import the project into IDE and run the `main` method in the project to start the application. In the console, you can see the log about startup as follows:

```
2018-05-12 13:12:05.868  INFO 76572 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'SpringMvcSofaTracerFilter' to urls: [/*]
2018-05-12 13:12:06.543  INFO 76572 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/helloZipkin]}" onto public java.util.Map<java.lang.String, java.lang.Object> com.alipay.sofa.tracer.examples.zipkin.controller.SampleRestController.helloZipkin(java.lang.String)
2018-05-12 13:12:07.164  INFO 76572 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
```	

You can access the REST service by typing [http://localhost:8080/helloZipkin](http://localhost:8080/helloZipkin) into your browser. The result is similar to the following:

```json
{
	content: "Hello, SOFATracer Zipkin Remote Report!",
	id: 1,
	success: true
}
```	

## View Zipkin UI

Open the Zipkin server interface, assuming that the address of the Zipkin server we deployed is `http://zipkin-cloud-3.host.net:9411`, open the URL and search for `zipkin` (since our local access address is localhost:8080/helloZipkin), you can see the link map shown.

## SOFATracer in Spring Project 

For a typical Spring project, we usually use tomcat/jetty as the servlet container to launch the application. Specific project reference [Use SOFATracer in Spring project](https://github.com/glmapper/tracer-zipkin-plugin-demo)

Attachment: [Source code](https://github.com/sofastack/sofa-tracer/tree/master/tracer-samples/tracer-sample-with-zipkin) for remote data reporting to Zipkin. 
Prerequisite: You need to configure and set up the Zipkin server with reference to [this document](https://zipkin.io/).


