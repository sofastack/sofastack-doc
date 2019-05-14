# 部署
## 环境准备
要使用 SOFARegistry，需要先准备好基础环境，SOFARegistry 依赖以下环境：
* Linux/Unix/Mac/Windows
* JDK8
* 需要采用 Apache Maven 3.2.5 或者以上的版本来编译


## 两种部署模式
* 集成部署模式
  * 将 meta/data/session 三个角色打包集成在一个 jvm 里运行，可单机或集群部署，部署简单。
* 独立部署模式
  * 将 meta/data/session 三个角色分开部署，每个角色都可以单机或集群部署，可根据实际情况为每个角色部署不同的数量。
  * 生产环境建议使用这种部署模式。

## 部署步骤
### 1. 下载源码和编译打包
#### 1.1 下载源码

```bash
git clone https://github.com/sofastack/sofa-registry.git
cd sofa-registry
```

#### 1.2 编译打包

```bash
mvn clean package -DskipTests
```

### 2. 部署注册中心
#### 2.1 集成部署模式
集成部署模式，是将 meta/data/session 三个角色打包集成在一个 JVM 里运行，可单机或集群部署。

##### 2.1.1 单机部署
集成部署的单机部署模式可以直接参考[快速开始-服务端部署](./Server-QuickStart)部分。

##### 2.1.2 集群部署
* 解压 registry-integration.tgz，并修改配置文件

集群部署，即搭建2台以上的集群，建议至少使用3台（注意：目前不支持在同一台机器部署多个 SOFARegistry，因此您必须有3台不同的机器）。在每一台机器上的部署方法同上：

```bash
cp server/distribution/integration/target/registry-integration.tgz <somewhere>
cd <somewhere> && mkdir registry-integration 
tar -zxvf registry-integration.tgz -C registry-integration
```

区别是每台机器在部署时需要修改 *conf/application.properties* 配置：

```bash
# 将3台机器的ip或hostname配置到下方(填入的hostname会被内部解析为ip地址)
nodes.metaNode=DefaultDataCenter:<hostname1>,<hostname2>,<hostname3>
nodes.localDataCenter=DefaultDataCenter
nodes.localRegion=DefaultZone
```

* 启动 registry-integration

每台机器都修改以上配置文件后，按照“单机部署”的步骤去启动 registry-integration 即可。
  * Linux/Unix/Mac：sh bin/startup.sh。
  * Windows: 双击 bin 目录下的 startup.bat 运行文件。 
* 确认运行状态：对每一台机器，都可访问三个角色提供的健康监测api，或查看日志 *logs/registry-startup.log*

```bash
# 查看meta角色的健康检测接口：(3台机器，有1台是Leader，其他2台是Follower)
$ curl http://localhost:9615/health/check
{"success":true,"message":"... raftStatus:Leader"}

# 查看data角色的健康检测接口：
$ curl http://localhost:9622/health/check
{"success":true,"message":"... status:WORKING"}

# 查看session角色的健康检测接口：
$ curl http://localhost:9603/health/check
{"success":true,"message":"..."}
```

## 2.2 独立部署模式   
独立部署模式，是将 meta/data/session 三个角色分开部署，每个角色都可以单机或集群部署，可根据实际情况为每个角色部署不同的数量，生产环境推荐使用这种部署模式。

以下介绍332模式(即 3台meta + 3台data + 2台session)的部署步骤。

#### 2.2.1 部署meta
* 解压 registry-meta.tgz，并修改配置文件

在3台机器上部署 meta 角色。在每一台机器上的部署方法如下：

```bash
cp server/distribution/meta/target/registry-meta.tgz <somewhere>
cd <somewhere> && mkdir registry-meta 
tar -zxvf registry-meta.tgz -C registry-meta
```

每台机器在部署时需要修改 *conf/application.properties* 配置：

```bash
# 将3台meta机器的ip或hostname配置到下方(填入的hostname会被内部解析为ip地址)
nodes.metaNode=DefaultDataCenter:<meta_hostname1>,<meta_hostname2>,<meta_hostname3>
nodes.localDataCenter=DefaultDataCenter
```

* 启动 registry-meta
  * Linux/Unix/Mac：sh bin/startup.sh。
  * Windows: 双击 bin 目录下的 startup.bat 运行文件。 
* 确认运行状态：对每一台机器，都可访问meta提供的健康监测api，或查看日志 logs/registry-startup.log

```bash
# 查看 meta 角色的健康检测接口：(3台机器，有1台是 Leader，其他2台是 Follower)
$ curl http://localhost:9615/health/check
{"success":true,"message":"... raftStatus:Leader"}
```

#### 2.2.2 部署data
* 解压 registry-data.tgz，并修改配置文件

在3台机器上部署 data 角色。在每一台机器上的部署方法如下：

```bash
cp server/distribution/data/target/registry-data.tgz <somewhere>
cd <somewhere> && mkdir registry-data 
tar -zxvf registry-data.tgz -C registry-data
```

每台机器在部署时需要修改 *conf/application.properties* 配置：

```bash
# 将3台 meta 机器的 ip 或 hostname 配置到下方(填入的 hostname 会被内部解析为 ip 地址)
nodes.metaNode=DefaultDataCenter:<meta_hostname1>,<meta_hostname2>,<meta_hostname3>
nodes.localDataCenter=DefaultDataCenter
data.server.numberOfReplicas=1
```

* 启动 registry-data
  * Linux/Unix/Mac：sh bin/startup.sh。
  * Windows: 双击 bin 目录下的 startup.bat 运行文件。 
* 确认运行状态：对每一台机器，都可访问data提供的健康监测api，或查看日志 *logs/registry-startup.log*

```bash
# 查看 data 角色的健康检测接口：
$ curl http://localhost:9622/health/check
{"success":true,"message":"... status:WORKING"}
```

#### 2.2.3 部署session
* 解压 registry-session.tgz，并修改配置文件

在2台机器上部署 session 角色。在每一台机器上的部署方法如下：

```bash
cp server/distribution/session/target/registry-session.tgz <somewhere>
cd <somewhere> && mkdir registry-session 
tar -zxvf registry-session.tgz -C registry-session
```

每台机器在部署时需要修改 *conf/application.properties* 配置：

```bash
# 将3台 meta 机器的 ip 或 hostname 配置到下方(填入的 hostname 会被内部解析为 ip 地址)
nodes.metaNode=DefaultDataCenter:<meta_hostname1>,<meta_hostname2>,<meta_hostname3>
nodes.localDataCenter=DefaultDataCenter
nodes.localRegion=DefaultZone
```

* 启动 registry-session
  * Linux/Unix/Mac：sh bin/startup.sh。
  * Windows: 双击 bin 目录下的 startup.bat 运行文件。 
* 确认运行状态：对每一台机器，都可访问session提供的健康监测api，或查看日志 *logs/registry-startup.log*

```bash
# 查看session角色的健康检测接口：
$ curl http://localhost:9603/health/check
{"success":true,"message":"..."}
```
