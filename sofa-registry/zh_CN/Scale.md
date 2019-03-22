# 扩缩容

## 1. 集成部署模式

### 1.1 扩容 registry-integration

假设目前已经部署了 3 台 registry-integration，分别是 node1/node2/node3，扩容的新节点是 node4。

**操作步骤：**

**第一步. 部署新的 registry-integration 节点**

首先参考[部署](./Deployment)文档，将 `registry-integration.tgz` 在新节点 node4 上部署起来，值得注意的是，node4 需要将 nodes.metaNode 配置项指定为4台机器的地址列表：

```bash
nodes.metaNode=DefaultDataCenter:<node1>,<node2>,<node3>,<node4>
```

在这一步中，node4启动完成后，访问 `curl http://<node4>:9615/health/check` 状态显示是不健康，因为 node4 尚未加入集群，要加入集群，需要做第二步。

**第二步. 调用 changePeer 使新节点加入集群**

对已经存在的 node1/node2/node3 的任意一台，执行“修改节点列表”的运维命令，将原有由 node1/node2/node3 构成的集群，改为 node1/node2/node3/node4 集群：

```bash
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>,<node3>,<node4>"
```

做完这一步之后，访问 `curl http://<node4>:9615/health/check` 状态显示应当是健康。

### 1.2 缩容 registry-integration

假设集群目前有3台机器 node1/node2/node3，需要缩容 node3。

#### 1.2.1 平滑缩容

**操作步骤：**

**第一步. 调用 changePeer 移除节点**

对 node1/node2 的任意一台，执行“修改节点列表”的运维命令，将集群列表由“node1/node2/node3”改为“node1/node2”，即把node3移除出地址列表：

```bash
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>"
```

做完这一步之后，访问 `curl http://<node3>:9615/health/check` 状态显示应当是不健康的，因为 node3 已经被踢出了集群。

**第二步.关闭 node3 **

这一步可选，因为 node3 已经被移除集群了，所以即便 node3 还在运行，也对原集群不影响。

#### 1.2.2 宕机处理
假设 node3 已经宕机，也需要将 node3 移除出集群

**操作步骤：**

**第一步. 调用 changePeer 移除节点**

对 node1/node2 的任意一台，执行“修改节点列表”的运维命令，将集群列表由“node1/node2/node3”改为“node1/node2”，即把 node3 移除出地址列表：

```bash
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>"
```

## 2. 独立部署模式

### 2.1 扩容 registry-meta

假设目前已经部署了3台 registry-meta ，分别是 metaNode1/metaNode2/metaNode3，扩容的新节点是 metaNode4.

**操作步骤：**

**第一步. 部署新的 registry-meta 节点**

首先参考[部署](./Deployment)文档，将 `registry-meta.tgz` 在新节点 metaNode4 上部署起来，值得注意的是，metaNode4 需要将 nodes.metaNode 配置项指定为4台机器的地址列表：

```bash
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>,<metaNode4>
```

在这一步中，metaNode4 启动完成后，访问 `curl http://localhost:9615/health/check` 状态显示是不健康，因为 metaNode4 尚未加入集群，要加入集群，需要做第二步。

**第二步. 调用 changePeer 使新节点加入集群**

对已经存在的 metaNode1/metaNode2/metaNode3 的任意一台，执行“修改节点列表”的运维命令，将原有由 metaNode1/metaNode2/metaNode3 构成的集群，改为 metaNode1/metaNode2/metaNode3/metaNode4 集群：

```bash
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>,<metaNode3>,<metaNode4>"
```

做完这一步之后，访问 `curl http://localhost:9615/health/check` 状态显示应当是健康。

### 2.2 缩容 registry-meta 

假设集群目前有3台机器 metaNode1/metaNode2/metaNode3，需要缩容 metaNode3。

#### 2.2.1 平滑缩容

**操作步骤：**

**第一步. 调用 changePeer 移除节点**

对 metaNode1/metaNode2 的任意一台，执行“修改节点列表”的运维命令，将集群列表由“metaNode1/metaNode2/metaNode3”改为“metaNode1/metaNode2”，即把 metaNode3 移除出地址列表：

```bash
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>"
```

做完这一步之后，访问 `curl http://<metaNode3>:9615/health/check` 状态显示应当是不健康的，因为metaNode3已经被踢出了集群。

**第二步. 关闭 metaNode3**

这一步可选，因为 metaNode3 已经被移除集群了，所以即便 metaNode3 还在运行，也对原集群不影响。

#### 2.2.2 宕机处理
假设 metaNode3 已经宕机，也需要将 metaNode3 移除出集群。

**操作步骤：**

**第一步. 调用 changePeer 移除节点**

对 metaNode1/metaNode2 的任意一台，执行“修改节点列表”的运维命令，将集群列表由“metaNode1/metaNode2/metaNode3”改为“metaNode1/metaNode2”，即把 metaNode3 移除出地址列表：

```bash
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>"
```

### 2.3 扩容 registry-data
扩容 registry-data 比较简单，只要配置相应的 metaNode 列表即可加入集群，不需要执行 changePeer。

**操作步骤：**

**第一步. 部署新的 registry-data 节点**

首先参考[部署](./Deployment)文档，将 `registry-data.tgz` 在新节点 newDataNode 上部署起来，只要 newDataNode 将 nodes.metaNode 配置项指定为和其他 registry-data 一样的 metaNode 机器列表即可：

```bash
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>
```

### 2.4 缩容 registry-data
缩容 registry-data 比较简单，只要关停相应的 dataNode 节点即可，dataNode 会自动被移除集群(因心跳过期)，不需要执行 changePeer。

### 2.5 扩容 registry-session
扩容 registry-session 比较简单，只要配置相应的 metaNode 列表即可加入集群，不需要执行 changePeer。

**操作步骤：**

**第一步. 部署新的 registry-session 节点**

首先参考[部署](./Deployment)文档，将 `registry-session.tgz` 在新节点 newSessionNode 上部署起来，只要 newSessionNode 将 nodes.metaNode 配置项指定为和其他 registry-session 一样的 metaNode 机器列表即可：

```bash
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>
```

### 2.6 缩容 registry-session

缩容 registry-session 比较简单，只要关停相应的 sessionNode 节点即可，sessionNode 会自动被移除集群(因心跳过期)，不需要执行 changePeer。

