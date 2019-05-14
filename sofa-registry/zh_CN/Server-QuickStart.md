# 服务端部署
## 部署模式
SOFARegistry 支持两种部署模式，分别是集成部署模式及独立部署模式，本文将介绍最简单的单节点集成部署模式，更多更详细的部署模式介绍可以查看[部署文档](./Deployment)。

## 部署步骤

### 1. 下载源码或者安装包

#### 下载源码方式

```bash
git clone https://github.com/sofastack/sofa-registry.git
cd sofa-registry
mvn clean package -DskipTests
cp server/distribution/integration/target/registry-integration.tgz <somewhere>
cd <somewhere> && mkdir registry-integration 
tar -zxvf registry-integration.tgz -C registry-integration
cd registry-integration
```

#### 下载安装包方式
您可以从 [release页面](https://github.com/sofastack/sofa-registry/releases) 下载最新的 registry-integration-$version.tar.gz 包。

```bash
mkdir registry-integration 
tar -zxvf registry-integration-$version.tar.gz -C registry-integration
cd registry-integration
```

### 2. 启动 registry-integration
#### Linux/Unix/Mac
启动命令：`sh bin/startup.sh`
#### Windows
双击 bin 目录下的 startup.bat 运行文件。 

### 3. 确认运行状态
可访问三个角色提供的健康监测 API，或查看日志 logs/registry-startup.log：

```bash
# 查看meta角色的健康检测接口：
$ curl http://localhost:9615/health/check
{"success":true,"message":"... raftStatus:Leader"}

# 查看data角色的健康检测接口：
$ curl http://localhost:9622/health/check
{"success":true,"message":"... status:WORKING"}

# 查看session角色的健康检测接口：
$ curl http://localhost:9603/health/check
{"success":true,"message":"..."}
```
