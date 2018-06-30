### 简介
本小节意在演示如何使用 Jarslink2.0 动态控制 Biz 包的生命周期，完成 Biz 包的安装、卸载、查询操作。

### 演示
参考[如何改造普通 Spring Boot 应用](./jarslink-app-demo.md)，改造后的 spring-boot-transform-sample 工程已经集成了 Jarslink2.0 组件，执行该应用打包生成的 Ark 包，即可运行时动态安装卸载应用。

+ java -jar 启动 spring-boot-transform-sample 应用 Ark 包
+ telnet localhost 1234 进入 Jarslink2.0 指令交互界面，形如：  
  > telnet localhost 1234
  > Trying 127.0.0.1...
  > Connected to localhost.
  > Escape character is '^]'.
  > sofa-ark>
+ 执行 check -b 查询指令，执行结果形如：
  > sofa-ark>check -b
  > Biz count=1
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0',       bizState='activated'
  >
  > sofa-ark>
+ 参考[如何改造普通 Spring Boot 应用](./jarslink-app-demo.md)，创建一个任意的非 Web 类型的 SOFABoot 应用，并将该应用打包成 Biz 包，执行 install -b 安装命令，执行结果形如：
  > sofa-ark>install -b file:///Users/qilong.zql/Desktop/test-ark-biz.jar
  > Biz:'test-biz:1.0.0' is installing.
  >
  > sofa-ark>

+ 再次执行 check -b 查询指令，执行结果形如：
  > sofa-ark>check -b
  > Biz count=2
  > bizName='test-biz', bizVersion='1.0.0', bizState='activated'
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0', bizState='activated'
  > 
  > sofa-ark>

+ 执行 uninstall -b -n -v 卸载指令，执行结果形如：
  > sofa-ark>uninstall -b -n test-biz -v 1.0.0
  > Uninstall biz:'test-biz:1.0.0' success.
  >
  > sofa-ark>

+ 再次执行 check -b 查询指令，执行结果形如：
  > sofa-ark>check -b
  > Biz count=1
  > bizName='spring-boot-transform-sample', bizVersion='1.0.0',   bizState='activated'
  > 
  > sofa-ark>