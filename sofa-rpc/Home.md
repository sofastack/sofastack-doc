### 产品描述

SOFARPC 是一款基于 Java 实现的 RPC 服务框架。

SOFARPC 为应用之间提供远程服务调用能力，具有高可伸缩性，高容错性。 

提供负载均衡，流量转发，链路追踪，链路数据透传，故障剔除等功能。 

支持 [bolt](https://github.com/alipay/sofa-bolt)，rest，dubbo 协议进行通信。其中 bolt 是蚂蚁金融服务集团开放的基于 Netty 开发的网络通信框架。

### 实现原理

![image.png | left | 748x404](./resources/home_1.png)

1. 当一个 SOFARPC 的应用启动的时候，如果发现当前应用需要发布 RPC 服务的话，那么 SOFARPC 会将这些服务注册到服务注册中心上。如图中 Service 指向 Registry。
2. 当引用这个服务的 SOFARPC 应用启动时，会从服务注册中心订阅到相应服务的元数据信息。服务注册中心收到订阅请求后，会将发布方的元数据列表实时推送给服务引用方。如图中 Registry 指向 Reference。
3. 当服务引用方拿到地址以后，就可以从中选取地址发起调用了。如图中 Reference 指向 Service。

