# Pilot介绍

SOFA mesh 项目 fork 了 istion 项目，对 pilot 的能力进行增强，目前在进行中的增强主要集中在下面三个方面。

- 支持 zookeeper 作为注册中心， 并在此基础上支持 SOFA, DUBBO 等使用 zk 作为注册中心的微服务框架。
- 支持通用协议框架，使用一个通用协议，在 k8s dns 的基础上同时支持多种协议。
- 新增 register agent，支持 SOFA，DUBBO和HSF的容器模型，即支持单个应用注册多个服务实例。