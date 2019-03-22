# 服务端部署
## 部署模式
SOFARegistry 支持两种部署模式，分别是集成部署模式及独立部署模式，本文将介绍最简单的单节点集成部署模式，更多更详细的部署模式介绍可以查看[部署文档](https://yuque.antfin-inc.com/sofa-open/sofa-registry/nwbmb4)。

## 部署步骤

### 1. 下载源码
```bash
git clone https://github.com/alipay/sofa-registry.git
cd sofa-registry
```

### 2. 编译打包
```bash
mvn clean package -DskipTests
```
### 3. 解压 registry-integration.tgz
```bash
cp server/distribution/integration/target/registry-integration.tgz <somewhere>
cd <somewhere> && mkdir registry-integration 
tar -zxvf registry-integration.tgz -C registry-integration
```

### 4. 启动 registry-integration
#### Linux/Unix/Mac
启动命令：sh bin/startup.sh 
#### Windows
双击 bin 目录下的 startup.bat 运行文件。 

### 5. 确认运行状态
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
