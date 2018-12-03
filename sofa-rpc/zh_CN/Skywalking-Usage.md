## Skywaking 支持

SOFARPC 在5.4.0 及之后的版本中，已经支持 Skywalking 的链路分析的功能，用户可以根据需要进行使用，其中Skywalking 的版本
要求`6.0.0-alpha`及以上。本文档，不涉及后端的部署，如有需要，可查看 Skywalking 官方文档。


### 安装 Java agent
1.在下载的 Skywalking 的release 包中找到 `agent` 目录。

2.在`config/agent.config` 中设置 `agent.service_name`，可以是任何英文字符，一般可以设置为自己的系统名。

3.在`config/agent.config` 中设置 `collector.backend_service` Skywalking 的后端地址，默认指向 `127.0.0.1:11800`,这个是为了本地验证的。

4.给应用程序添加 `-javaagent:/path/to/skywalking-package/agenxt/skywalking-agent.jar`，其中注意，一定要放在 `-jar` 参数之前。
Agent 在 kywalking 的 [官方 release 包](http://skywalking.apache.org/downloads/). 新的目录结构如下.
```
+-- agent
    +-- activations
         apm-toolkit-log4j-1.x-activation.jar
         apm-toolkit-log4j-2.x-activation.jar
         apm-toolkit-logback-1.x-activation.jar
         ...
    +-- config
         agent.config  
    +-- plugins
         sofa-rpc-plugin-6.0.0-alpha.jar
         apm-feign-default-http-9.x.jar
         apm-httpClient-4.x-plugin.jar
         .....
    skywalking-agent.jar
```
注意，确保plugins/sofa-rpc-plugin-**.jar 文件存在。

5.启动应用程序，经过一段时间RPC调用后，可以查看 UI 来观察链路。


### 更多

更多文档请参考

[Skywalking Agent 安装文档](https://github.com/apache/incubator-skywalking/blob/master/docs/en/setup/service-agent/java-agent/README.md)
[Skywalking Backend 部署文档](https://github.com/apache/incubator-skywalking/blob/master/docs/en/setup/backend/backend-ui-setup.md)