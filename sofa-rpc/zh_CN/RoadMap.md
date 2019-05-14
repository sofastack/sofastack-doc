# 发展路线



![[Roadmap]()](./resources/roadmap.png)


## 任务列表

部分内部已有的功能特性，待**内部整理完毕后**随各个迭代放出。

如果**还没有实现的功能特性**会列在下面的表格中，欢迎大家认领任务，[参与贡献](https://github.com/sofastack/sofa-rpc/wiki/Contributing)。

| 类型 | 任务              | 困难度 | 认领人及时间      | 计划完成时间 | 进度 | 相关Issue |
|:----|:------------------|:-----:|:------------------|:------------|:----|:--------:|
| 文档 | 文档翻译           |   低   |                 |            |     |         | 
| 代码 | 弹性长连接管理方式  |   低  |                  |            |     | [#56](https://github.com/sofastack/sofa-rpc/issues/56) | 
| 代码 | etcd注册中心实现   |   中   | [@wynn5a](https://github.com/wynn5a)<br>2018-6    |            |      | [#153](https://github.com/sofastack/sofa-rpc/issues/153)    |         | 
| 代码 | eureka注册中心实现 |   中   | [@liufeiit](https://github.com/liufeiit)<br>2018-4 |            |     | [#52](https://github.com/sofastack/sofa-rpc/issues/52)        | 
| 代码 | gRPC 支持         |   高   |                  |            |     | [#57](https://github.com/sofastack/sofa-rpc/issues/57) | 
| 代码 | CXF 协议          |   高   |                  |            |     | [#58](https://github.com/sofastack/sofa-rpc/issues/58)  | 
| 代码 | TLS 支持          |   高   |                  |            |     |   | 

## 版本迭代计划

### v5.5.0

- JSON 序列化支持
- H2的TLS安全支持
- 弹性连接池
- hystrix集成
- Consul注册中心支持

### v5.6.0

- grpc 通讯层支持
- etcd注册中心支持
- SofaMesh支持
- BOLT 版本协商与 CRC 校验

### v5.7.0

- Telnet 内置指令支持
- SpringBoot 2.0 支持
- Mock功能支持
- 加密功能支持

### v5.8.0

- 授权支持
- SofaRegistry 支持
- Reactive 支持
