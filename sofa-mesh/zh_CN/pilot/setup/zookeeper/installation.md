# 安装

在非 Kubernetes 环境下使用 Istio 需要达成以下的关键任务：

1. 为 Istio 控制平面配置 Istio API server，也可以通过 memostore 的方式启动 Pilot 用作演示用途。
2. 给所有微服务实例手工添加 SOFA MOSN，并以 Sidecar 模式启动。
3. 确保请求都通过 SOFA MOSN 进行路由。

## 设定控制平面

Istio 控制平面由四个主要的服务组成：Pilot、Mixter、Citadel 以及 API server。

### API server

Istio's API server (基于 kubernetes API server) 提供了配置管理和基于角色的访问控制。API server 需要 etcd 集群作为底层的持久化存储。

#### 本地安装

使用如下的 docker compose file 安装一个用于 POC 目的的 API server：

```YAML
version: '2'
services:
  etcd:
    image: quay.io/coreos/etcd:latest
    networks:
      default:
        aliases:
          - etcd
    ports:
      - "4001:4001"
      - "2380:2380"
      - "2379:2379"
    environment:
      - SERVICE_IGNORE=1
    command: [
              "/usr/local/bin/etcd",
              "-advertise-client-urls=http://0.0.0.0:2379",
              "-listen-client-urls=http://0.0.0.0:2379"
             ]

  istio-apiserver:
    image: gcr.io/google_containers/kube-apiserver-amd64:v1.7.3
    networks:
      default:
        aliases:
          - apiserver
    ports:
      - "8080:8080"
    privileged: true
    environment:
      - SERVICE_IGNORE=1
    command: [
               "kube-apiserver", "--etcd-servers", "http://etcd:2379",
               "--service-cluster-ip-range", "10.99.0.0/16",
               "--insecure-port", "8080",
               "-v", "2",
               "--insecure-bind-address", "0.0.0.0"
             ]
```

### 其他控制平面组件

目前 SOFA MOSN 还没有集成 Pilot 之外的其他组件，因此我们暂时无需安装 Mixer、Citadel 等组件。

### 为微服务实例添加 SOFA MOSN Sidecar

微服务应用的每个实例都必须有个伴生的 SOFA MOSN 实例。
