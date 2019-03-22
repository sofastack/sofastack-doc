# 扩缩容

## 1. 集成部署模式

### 1.1 扩容registry-integration

假设目前已经部署了3台registry-integration，分别是node1/node2/node3，扩容的新节点是node4。

**操作步骤：**

**a. 部署新的registry-integration节点**

首先参考《部署》文档，将_registry-integration.tgz_在新节点node4上部署起来，值得注意的是，node4需要将nodes.metaNode配置项指定为4台机器的地址列表：

```text
nodes.metaNode=DefaultDataCenter:<node1>,<node2>,<node3>,<node4>
```

在这一步中，node4启动完成后，访问 `curl http://<node4>:9615/health/check` 状态显示是不健康，因为node4尚未加入集群，要加入集群，需要做第二步。

**b. 调用changePeer使新节点加入集群**

对已经存在的node1/node2/node3的任意一台，执行“修改节点列表”的运维命令，将原有由node1/node2/node3构成的集群，改为node1/node2/node3/node4集群：

```shell
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>,<node3>,<node4>"
```

做完这一步之后，访问 `curl http://<node4>:9615/health/check` 状态显示应当是健康。

### 1.2 缩容registry-integration

假设集群目前有3台机器 node1/node2/node3，需要缩容node3。
#### 1.2.1 平滑缩容
**操作步骤：**

**a. 调用changePeer移除节点**

对node1/node2的任意一台，执行“修改节点列表”的运维命令，将集群列表由“node1/node2/node3”改为“node1/node2”，即把node3移除出地址列表：

```shell
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>"
```

做完这一步之后，访问 `curl http://<node3>:9615/health/check` 状态显示应当是不健康的，因为node3已经被踢出了集群。

**b.关闭node3**

这一步可选，因为node3已经被移除集群了，所以即便node3还在运行，也对原集群不影响。

#### 1.2.2 宕机处理
假设node3已经宕机，也需要将node3移除出集群

**操作步骤：**<br />**a. 调用changePeer移除节点**

对node1/node2的任意一台，执行“修改节点列表”的运维命令，将集群列表由“node1/node2/node3”改为“node1/node2”，即把node3移除出地址列表：

```shell
curl -X POST "http://<node1>:9615/manage/changePeer" -d "ipAddressList=<node1>,<node2>"
```

## 2. 独立部署模式
### 2.1 扩容registry-meta
假设目前已经部署了3台registry-meta，分别是metaNode1/metaNode2/metaNode3，扩容的新节点是metaNode4.

**操作步骤：**

**a. 部署新的registry-meta节点**

首先参考《部署》文档，将_registry-meta.tgz_在新节点metaNode4上部署起来，值得注意的是，metaNode4需要将nodes.metaNode配置项指定为4台机器的地址列表：

```text
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>,<metaNode4>
```

在这一步中，metaNode4启动完成后，访问 `curl http://localhost:9615/health/check` 状态显示是不健康，因为metaNode4尚未加入集群，要加入集群，需要做第二步。

**b. 调用changePeer使新节点加入集群**

对已经存在的 metaNode1/metaNode2/metaNode3 的任意一台，执行“修改节点列表”的运维命令，将原有由 metaNode1/metaNode2/metaNode3 构成的集群，改为 metaNode1/metaNode2/metaNode3/metaNode4 集群：

```shell
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>,<metaNode3>,<metaNode4>"
```

做完这一步之后，访问 `curl http://localhost:9615/health/check` 状态显示应当是健康。

### 2.2 缩容registry-meta
假设集群目前有3台机器 metaNode1/metaNode2/metaNode3，需要缩容metaNode3。

#### 2.2.1 平滑缩容
**操作步骤：**

**a. 调用changePeer移除节点**

对metaNode1/metaNode2的任意一台，执行“修改节点列表”的运维命令，将集群列表由“metaNode1/metaNode2/metaNode3”改为“metaNode1/metaNode2”，即把metaNode3移除出地址列表：

```shell
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>"
```

做完这一步之后，访问 `curl http://<metaNode3>:9615/health/check` 状态显示应当是不健康的，因为metaNode3已经被踢出了集群。

**b.关闭metaNode3**

这一步可选，因为metaNode3已经被移除集群了，所以即便metaNode3还在运行，也对原集群不影响。

#### 2.2.2 宕机处理
假设metaNode3已经宕机，也需要将metaNode3移除出集群。

**操作步骤：**

**a. 调用changePeer移除节点**

对metaNode1/metaNode2的任意一台，执行“修改节点列表”的运维命令，将集群列表由“metaNode1/metaNode2/metaNode3”改为“metaNode1/metaNode2”，即把metaNode3移除出地址列表：

```shell
curl -X POST "http://<metaNode1>:9615/manage/changePeer" -d "ipAddressList=<metaNode1>,<metaNode2>"
```

### 2.3 扩容registry-data
扩容registry-data比较简单，只要配置相应的metaNode列表即可加入集群，不需要执行changePeer。

**操作步骤：**

**a. 部署新的registry-data节点**

首先参考《部署》文档，将_registry-data.tgz_在新节点newDataNode上部署起来，只要newDataNode将nodes.metaNode配置项指定为和其他registry-data一样的metaNode机器列表即可：

```html
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>
```

### 2.4 缩容registry-data
缩容registry-data比较简单，只要关停相应的dataNode节点即可，dataNode会自动被移除集群(因心跳过期)，不需要执行changePeer。

### 2.5 扩容registry-session
扩容registry-session比较简单，只要配置相应的metaNode列表即可加入集群，不需要执行changePeer。

**操作步骤：**

**a. 部署新的registry-session节点**

首先参考《部署》文档，将_registry-session.tgz_在新节点newSessionNode上部署起来，只要newSessionNode将nodes.metaNode配置项指定为和其他registry-session一样的metaNode机器列表即可：

```html
nodes.metaNode=DefaultDataCenter:<metaNode1>,<metaNode2>,<metaNode3>
```

### 2.6 缩容registry-session
缩容registry-session比较简单，只要关停相应的sessionNode节点即可，sessionNode会自动被移除集群(因心跳过期)，不需要执行changePeer。

