# SOFADashboard 简介

SOFADashboard 致力于对 SOFA 框架中组件进行统一管理，包括服务治理、SOFAArk 管控等。SOFADashboard 本身所用技术栈均基于开源社区产品来开发构建，包括：Ant Design Pro、SOFABoot、Spring、MyBatis 等。

目前，SOFADashboard 中的服务治理、SOFAArk 管控等需要依赖于 Zookeeper，因此如果您需要使用 SOFADashboard 那么请确保 Zookeeper 服务可用；另外 SOFAArk 管控部署需要依赖 MySQL 进行资源数据存储，因此也需要保证 MySQL 可以正常使用。

## 架构简图

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*uVAiQKWS4G4AAAAAAAAAAABjARQnAQ)

SOFADashboard 目前服务治理与 SOFAArk 管控都是面向 Zookeeper 来编程实现的。

* SOFADashboard backend  : 对应 sofa-dashboard-backend 工程，是 SOFADashboard 的服务端工程，负责与 Zookeeper 和 MySQL 进行数据交互，并且为 SOFADashboard frontend 提供 rest 接口。
* SOFADashboard frontend : 对应 sofa-dashboard-frontend 工程，是 SOFADashboard 的前端工程，用于提供与用户交互的 UI 界面。
* app 应用
  * rpc provider : SOFARPC 的服务提供方，会将服务注册到 Zookeeper 上。
  * rpc consumer : SOFARPC 的服务消费方，会从 Zookeeper 上订阅服务。
  * client : SOFADashboard 客户端，引入 sofa-dashboard-client 包即可。目前仅提供将应用的健康检查状态及端口信息注册到 Zookeeper ，后面将会演化成 SOFABoot client，上报更丰富的应用数据。
  * ark-biz 宿主应用: 参考 [SOFAArk ](https://www.sofastack.tech/sofa-boot/docs/sofa-ark-ark-config)。
