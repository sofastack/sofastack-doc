# Pilot 简介

SOFA Mesh 项目 fork 了 Istio 项目，对 Pilot 的能力进行增强，目前在进行中的增强主要集中在下面三个方面：

- 支持 Zookeeper 作为注册中心，并在此基础上支持 SOFA、DUBBO 等使用 Zookeeper 作为注册中心的微服务框架。
- 支持通用协议框架，使用一个通用协议，在 Kubernetes DNS 的基础上同时支持多种协议。
- 新增 register agent，支持 SOFA、DUBBO 和 HSF 的容器模型，即支持单个应用注册多个服务实例。

