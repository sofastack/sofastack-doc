# 简介
## 项目简介
SOFARegistry 是蚂蚁金服开源的一个生产级、高时效、高可用的服务注册中心。SOFARegistry 最早源自于淘宝的 ConfigServer，十年来，随着蚂蚁金服的业务发展，注册中心架构已经演进至第五代。目前 SOFARegistry 不仅全面服务于蚂蚁金服的自有业务，还随着蚂蚁金融科技服务众多合作伙伴，同时也兼容开源生态。SOFARegistry 采用 AP 架构，支持秒级时效性推送，同时采用分层架构支持无限水平扩展。

## 产品特点
### 高可扩展性
采用分层架构、数据分片存储等方式，突破单机性能与容量瓶颈，接近理论上的“无限水平扩展”。经受过蚂蚁金服生产环境海量节点数与服务数的考验。

### 高时效性
借助 [SOFABolt](https://github.com/sofastack/sofa-bolt) 通信框架，实现基于TCP长连接的节点判活与推模式的变更推送，服务上下线通知时效性在秒级以内。

### 高可用性
不同于 Zookeeper、Consul、Etcd 等 CP 架构注册中心产品，SOFARegistry 针对服务发现的业务特点，采用 AP 架构，最大限度地保证网络分区故障下注册中心的可用性。通过集群多副本等方式，应对自身节点故障。

## 架构
服务注册中心分为四个角色，客户端（Client）、会话服务器（SessionServer）、数据服务器（DataServer）、元数据服务器（MetaServer），每个角色司职不同能力组合后共同提供对外服务能力，各部分关系和结构如下：

![SOFARegistry.svg](https://gw.alipayobjects.com/zos/basement_prod/a9b69b25-836f-4bbe-a32c-ec6148084f93.svg)

### Client
提供应用接入服务注册中心的基本 API 能力，应用系统通过依赖客户端 JAR 包，通过编程方式调用服务注册中心的服务订阅和服务发布能力。

### SessionServer
会话服务器，提供客户端接入能力，接受客户端的服务发布及服务订阅请求，并作为一个中间层将发布数据转发 DataServer 存储。SessionServer 可无限扩展以支持海量客户端连接。

### DataServer
数据服务器，负责存储客户端发布数据，数据存储按照数据 ID 进行一致性 hash 分片存储，支持多副本备份，保证数据高可用。DataServer 可无限扩展以支持海量数据量。

### MetaServer
元数据服务器，负责维护集群 SessionServer 和 DataServer 的一致列表，在节点变更时及时通知集群内其他节点。MetaServer 通过 [SOFAJRaft](https://github.com/sofastack/sofa-jraft) 保证高可用和一致性。


