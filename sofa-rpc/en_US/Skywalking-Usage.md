## Skywaking support

Since SOFARPC 5.4.0, the link analysis feature of Skywalking is supported. You can use it as needed. The Skywalking must be `6.0.0-alpha` and above. 

This document does not cover the backend deployment. If you need it, please refer to the official Skywalking documentation.


### Install Java agent
1. Locate the `agent` directory in the downloaded Skywalking release package.

2. Set `agent.service_name` in `config/agent.config`, which can be any English character. Generally, it can be your own system name.

3. Set the `collector.backend_service` Skywalking backend address in `config/agent.config`, which defaults to `127.0.0.1:11800`. It is used for local verification.

4. Add `-javaagent:/path/to/skywalking-package/agenxt/skywalking-agent.jar` to the application, which must be placed before the `-jar` parameter.
The `skywalking-agent` can be gotten in [official release package](http://skywalking.apache.org/downloads/). The new directory structure is as follows:
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
Note: Ensure that the `plugins/sofa-rpc-plugin-**.jar` file exists.

5. Start the application. After a period of RPC calls, you can view the UI to observe the calling link.


### More

For more relevant documents, please refer to

[Skywalking Agent installation documentation](https://github.com/apache/incubator-skywalking/blob/master/docs/en/setup/service-agent/java-agent/README.md)
[Skywalking Backend deployment documentation](https://github.com/apache/incubator-skywalking/blob/master/docs/en/setup/backend/backend-ui-setup.md)