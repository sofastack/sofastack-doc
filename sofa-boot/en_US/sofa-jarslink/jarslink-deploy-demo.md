## Introduction
This section is intended to demonstrate how to use Jarslink 2.0 to dynamically control the life cycle of the Biz package and to complete its installation, uninstallation, and query.

## Demo
With reference to [How to reform common Spring Boot applications](./jarslink-app-demo.md), the reformed spring-boot-transform-sample project has integrated the Jarslink 2.0 component. By executing the Ark package that the application packaged and generated, you can dynamically install or uninstall the application during its running.

+ java -jar starts the spring-boot-transform-sample application Ark package.
+ telnet localhost 1234 enters the Jarslink 2.0 command interface, as follows:  
  > telnet localhost 1234  
  > Trying 127.0.0.1...  
  > Connected to localhost.  
  > Escape character is '^]'.  
  > sofa-ark>
+ Execute the check -b query command, and the result is as follows:
  > sofa-ark>check -b  
  > Biz count=1  
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0', bizState='activated'
  >
  > sofa-ark>
+ With reference to [How to reform a common Spring Boot application](./jarslink-app-demo.md), create any SOFABoot application of non-Web type, package it into a Biz package, and execute the install -b installation command, and the result is as follows:
  > sofa-ark>install -b file:///Users/qilong.zql/Desktop/test-ark-biz.jar  
  > Biz:'test-biz:1.0.0' is installing.
  >
  > sofa-ark>

+ Execute the check -b query command again, and the result is as follows:
  > sofa-ark>check -b  
  > Biz count=2  
  > bizName='test-biz', bizVersion='1.0.0', bizState='activated'  
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0', bizState='activated'
  > 
  > sofa-ark>

+ Execute the uninstall -b -n -v uninstallation command, and the result is as follows:
  > sofa-ark>uninstall -b -n test-biz -v 1.0.0  
  > Uninstall biz:'test-biz:1.0.0' success.
  >
  > sofa-ark>

+ Execute the check -b query command again, and the result is as follows:
  > sofa-ark>check -b  
  > Biz count=1  
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0', bizState='activated'
  > 
  > sofa-ark>
  
  For use of more commands, refer to [Interactive Commands](./jarslink-instruction).

