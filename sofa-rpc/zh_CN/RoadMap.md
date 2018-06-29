# 发展路线

***
> 目前Lookout, Tracer 等部分集成功能特性还在代码整理当中，敬请期待。
***
## 任务列表

部分内部已有的功能特性，待**内部整理完毕后**随各个迭代放出。

如果**还没有实现的功能特性**会列在下面的表格中，欢迎大家认领任务，[参与贡献](https://github.com/alipay/sofa-rpc/wiki/Contributing)。

| 类型 | 任务              | 困难度 | 认领人及时间      | 计划完成时间 | 进度 | 相关Issue |
|:----|:------------------|:-----:|:------------------|:------------|:----|:--------:|
| 文档 | 文档翻译           |   低   |                 |            |     |         | 
| 代码 | 弹性长连接管理方式  |   低  |                  |            |     | [#56](https://github.com/alipay/sofa-rpc/issues/56) | 
| 代码 | Consul注册中心实现 |   中   | [@Moriadry](https://github.com/Moriadry)<br>2018-4 |            |     | [#54](https://github.com/alipay/sofa-rpc/issues/54)   | 
| 代码 | etcd注册中心实现   |   中   | [@wynn5a](https://github.com/wynn5a)<br>2018-6    |            |      | [#153](https://github.com/alipay/sofa-rpc/issues/153)    |         | 
| 代码 | eureka注册中心实现 |   中   | [@liufeiit](https://github.com/liufeiit)<br>2018-4 |            |     | [#52](https://github.com/alipay/sofa-rpc/issues/52)        | 
| 代码 | gRPC 支持         |   高   |                  |            |     | [#57](https://github.com/alipay/sofa-rpc/issues/57) | 
| 代码 | SkyWalking 支持   |   中   |                  |            |     | [#33](https://github.com/alipay/sofa-rpc/issues/33)  | 
| 代码 | CXF 协议          |   高   |                  |            |     | [#58](https://github.com/alipay/sofa-rpc/issues/58)  | 
| 代码 | TLS 支持          |   高   |                  |            |     |   | 

## 版本迭代计划

### v5.5.0
- 增加 Bolt 协议下的版本协商机制和 CRC 校验。
- 增加对 TLS 安全的支持。
- 增加对 gRPC 通讯的支持。
- 增加对 etcd 注册中心的支持。
- 增加对 Consul 注册中心的支持。
- 增加对 SOFARegistry 注册中心的支持。