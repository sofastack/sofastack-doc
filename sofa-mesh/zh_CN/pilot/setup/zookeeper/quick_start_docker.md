# 使用 docker 快速开始

本文旨在描述如何使用 docker compose 快速开始安装和配置 istio。
SOFA Mosn 不仅可以支持 istio 标准的部署模式，也能支持单方面的 inbound sidecar，outbound sidecar的部署模式，满足用户的各种需求。

## 前置要求

- Docker
- Docker compose

## 安装步骤

1. 下载最新的 release 包
2. 解压安装文件，并且进入解压后的路径，安装路径包含：
- 示例应用路径 samples/。
- /bin 路径下应该能找到 istioctl 客户端可执行文件，istioctl 可用于创建路由规则和策略。
- 配置文件 istion.VERSION
3. 把 istio 的 bin 路径添加到系统的 PATH。比如，在 MacOS 或者 Linux 系统下执行如下命令：
    ```SHELL
    export PATH=$PWD/bin;$PATH
    ```
4. 拉起 istio 控制平面容器：
    ```SHELL
    docker-compose -f install/zookeeper/istio.yaml up -d
    ```
5. 确认所有 docker 容器都在运行中：
    ```SHELL
    docker ps -a
    ```
    如果 istio pilot 容器意外终止，确保运行 istioctl context-create 命令，并且重新执行上一个命令。
6. 配置 istioctl 使用 istio API server：
    ```SHELL
    istioctl context-create -context istio-local --api-server
    ```
## 部署应用程序

现在开始部署 sofa boot 示例程序，示例程序包含客户端和服务端，使用 bolt 协议进行通讯。

```SHELL
docker-compose up -f sofa-sample-spec.yaml up -d
```

## 卸载 istio

```
docker-compose up -f install/zookeeper/istio.yaml down
```