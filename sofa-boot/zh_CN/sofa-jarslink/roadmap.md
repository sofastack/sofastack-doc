## 发展路线
![RoadMap](../resources/jarslink-roadmap.png)

## 任务列表

下面表格记录了还没有实现的功能特性，欢迎大家认领任务，[参与贡献](./contribution)。

| 类型 | 任务 | 困难度 | 认领人及时间 | 计划完成时间 | 进度 | 相关 Issue |
|---|---|---|---|---|---|---|
|代码|支持客户端 API 形式管理多应用生命周期|中| | | |[#91](https://github.com/sofastack/sofa-jarslink/issues/91)|
|代码|支持多 Web 应用合并部署|难| | | |[#92](https://github.com/sofastack/sofa-jarslink/issues/92)|
|代码|支持 SOFARPC 完全卸载|难| | | | |
|代码|支持 Dubbo 远程转 JVM 内部调用|难| | | | |
|测试|提供 RPC 转 JVM 性能对比图|中| | | | |

## 版本迭代计划

### v2.1.0 
+ 支持客户端 API 形式管理多应用生命周期。
+ 支持多 Web 应用合并部署。

### v2.2.0
+ 支持 SOFARPC 完全卸载。
+ 针对Dubbo 框架，RPC 服务自动转化为内部 JVM 调用，目前只支持 SOFARPC 框架。（待调研，不确定，可能影响健康检查逻辑）
+ 提供 RPC 转 JVM 性能对比图。
