SOFAArk 容器使用了 logback 日志实现，并集成了 [sofa-common-tools](https://github.com/sofastack/sofa-common-tools)，日志相关配置可以参考[配置文档](./ark-config), 这里介绍 SOFAArk 三个日志文件：

+ sofa-ark/common-default.log
> sofa-ark 默认日志，打印 SOFAArk 启动日志等，大概内容如下：

```text
2019-03-12 15:08:55,758 INFO  main                             - Begin to start ArkServiceContainer
2019-03-12 15:08:56,290 INFO  main                             - Init Service: com.alipay.sofa.ark.container.session.StandardTelnetServerImpl
2019-03-12 15:08:56,311 INFO  main                             - Listening on port: 1234
2019-03-12 15:08:56,313 INFO  main                             - Init Service: com.alipay.sofa.ark.container.service.plugin.PluginDeployServiceImpl
2019-03-12 15:08:56,313 INFO  main                             - Init Service: com.alipay.sofa.ark.container.service.biz.BizDeployServiceImpl
2019-03-12 15:08:56,313 INFO  main                             - Init Service: com.alipay.sofa.ark.container.service.classloader.ClassLoaderServiceImpl
2019-03-12 15:08:56,317 INFO  main                             - Finish to start ArkServiceContainer
2019-03-12 15:08:56,338 INFO  main                             - Start to process pipeline stage: com.alipay.sofa.ark.container.pipeline.HandleArchiveStage
2019-03-12 15:08:56,349 INFO  main                             - Finish to process pipeline stage: com.alipay.sofa.ark.container.pipeline.HandleArchiveStage
2019-03-12 15:08:56,349 INFO  main                             - Start to process pipeline stage: com.alipay.sofa.ark.container.pipeline.RegisterServiceStage
2019-03-12 15:08:56,354 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.biz.BizManagerService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,354 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.biz.BizFactoryService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,355 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.plugin.PluginManagerService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,356 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.plugin.PluginFactoryService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,356 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.event.EventAdminService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,357 INFO  main                             - Service: com.alipay.sofa.ark.spi.service.registry.RegistryService publish by: ServiceProvider{provider='Ark Container', order=-2147483648} succeed
2019-03-12 15:08:56,360 INFO  main                             - Inject {field= bizManagerService} of {service= ServiceMetadata{service='com.alipay.sofa.ark.spi.service.biz.BizDeployer', provider='ServiceProvider{provider='Ark Container', order=100}'}} success!
```

+ sofa-ark/common-error.log
> sofa-ark 错误日志，打印 SOFAArk 容器运行时错误日志，例如 biz 启动失败日志等：

```text
2019-03-12 16:38:41,873 ERROR main                             - Start biz: Startup In IDE meet error
java.lang.reflect.InvocationTargetException: null
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at com.alipay.sofa.ark.bootstrap.MainMethodRunner.run(MainMethodRunner.java:48)
        at com.alipay.sofa.ark.container.model.BizModel.start(BizModel.java:207)
        at com.alipay.sofa.ark.container.service.biz.DefaultBizDeployer.deploy(DefaultBizDeployer.java:52)
        at com.alipay.sofa.ark.container.service.biz.BizDeployServiceImpl.deploy(BizDeployServiceImpl.java:55)
        at com.alipay.sofa.ark.container.pipeline.DeployBizStage.process(DeployBizStage.java:47)
        at com.alipay.sofa.ark.container.pipeline.StandardPipeline.process(StandardPipeline.java:75)
        at com.alipay.sofa.ark.container.ArkContainer.start(ArkContainer.java:135)
        at com.alipay.sofa.ark.container.ArkContainer.main(ArkContainer.java:96)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at com.alipay.sofa.ark.bootstrap.MainMethodRunner.run(MainMethodRunner.java:48)
        at com.alipay.sofa.ark.bootstrap.AbstractLauncher.launch(AbstractLauncher.java:97)
        at com.alipay.sofa.ark.bootstrap.AbstractLauncher.launch(AbstractLauncher.java:68)
        at com.alipay.sofa.ark.support.startup.SofaArkBootstrap.remain(SofaArkBootstrap.java:77)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at com.alipay.sofa.ark.support.thread.LaunchRunner.run(LaunchRunner.java:61)
        at java.lang.Thread.run(Thread.java:745)
Caused by: org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
```

+ sofa-ark/config-manage.log
> sofa-ark 动态日志配置，打印 SOFAArk 运行时接受的动态配置信息，大概内容如下：

```text
2019-03-01 16:16:36,583 INFO  main                             - Registry context path: /sofa-ark/demo with mode: PERSISTENT.
2019-03-01 16:16:36,818 INFO  Curator-ConnectionStateManager-0 - Reconnect to zookeeper, re-register config resource.
2019-03-01 16:16:36,839 WARN  main                             - Context path has exists in zookeeper, path=/sofa-ark/demo
2019-03-01 16:16:36,839 INFO  main                             - Registry context path: /sofa-ark/demo/xx.xx.xxx.xxx with mode: EPHEMERAL.
2019-03-01 16:16:36,850 INFO  main                             - Subscribe ip config: /sofa-ark/demo/xx.xx.xxx.xxx.
2019-03-01 16:17:20,215 INFO  main                             - Registry context path: /sofa-ark/demo with mode: PERSISTENT.
2019-03-01 16:17:20,297 INFO  Curator-ConnectionStateManager-0 - Reconnect to zookeeper, re-register config resource.
2019-03-01 16:17:20,319 WARN  main                             - Context path has exists in zookeeper, path=/sofa-ark/demo
2019-03-01 16:17:20,319 INFO  main                             - Registry context path: /sofa-ark/demo/xx.xx.xxx.xxx with mode: EPHEMERAL.
2019-03-01 16:17:20,327 INFO  main                             - Subscribe ip config: /sofa-ark/demo/xx.xx.xxx.xxx.
2019-03-01 16:17:20,414 INFO  main                             - Start to process init app config: app-two:2.0.0:activated?bizUrl=http://sofa.open.alipay.net:8090/sofa/ark/app-two/2.0.0/app-two-2.0.0-ark-biz.jar
2019-03-01 16:17:20,417 INFO  main                             - Execute biz operation: INSTALL app-two:2.0.0
2019-03-01 16:17:32,226 INFO  main                             - Registry context path: /sofa-ark/demo with mode: PERSISTENT.
2019-03-01 16:17:32,292 INFO  Curator-ConnectionStateManager-0 - Reconnect to zookeeper, re-register config resource.
2019-03-01 16:17:32,313 WARN  main                             - Context path has exists in zookeeper, path=/sofa-ark/demo
2019-03-01 16:17:32,313 INFO  main                             - Registry context path: /sofa-ark/demo/xx.xx.xxx.xxx with mode: EPHEMERAL.
2019-03-01 16:17:32,322 INFO  main                             - Subscribe ip config: /sofa-ark/demo/xx.xx.xxx.xxx.
2019-03-01 16:17:37,295 INFO  main                             - Start to process init app config: app-two:2.0.0:activated?bizUrl=http://sofa.open.alipay.net:8090/sofa/ark/app-two/2.0.0/app-two-2.0.0-ark-biz.jar
2019-03-01 16:17:37,298 INFO  main                             - Execute biz operation: INSTALL app-two:2.0.0
2019-03-01 16:18:24,291 INFO  main-EventThread                 - Receive app config data: , version is 34.
2019-03-01 16:18:24,352 INFO  SOFA-ARK-app-zookeeper-config-1-T1 - ConfigTask: app-zookeeper-config start to process config: 
2019-03-01 16:18:24,352 INFO  SOFA-ARK-app-zookeeper-config-1-T1 - Execute biz operation: UNINSTALL app-two:2.0.0
```