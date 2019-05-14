# Quick Start

这个 Quick Start 可以帮您快速在您的电脑上，下载、安装并使用 SOFADashboard。

## 环境准备

sofa-dashboard-backend 依赖 [Java](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 环境来运行。请确保是在以下运行环境可以正常使用:

* JDK 1.8+；[下载](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) & [配置](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)。
* Maven 3.2.5+；[下载](https://maven.apache.org/download.cgi) & [配置](https://maven.apache.org/settings.html)。

sofa-dashboard-frontend 使用了 [Ant Design Pro](https://github.com/ant-design/ant-design-pro) 脚手架，前端环境请参考 [Ant Design](https://github.com/ant-design/ant-design/blob/master/README-zh_CN.md)

### 数据库初始化

> Mysql 版本：5.6+

SOFAArk 管控需要依赖 MySQL 进行资源数据存储，工程目录下有一个 SofaDashboardDB.sql 脚本文件，可以通过执行这个脚本文件进行数据库表的初始化。

### Zookeeper 

> ZooKeeper 3.4.x and ZooKeeper 3.5.x

SOFADashboard 中的服务治理、SOFAArk 管控依赖于 Zookeeper，需要本地启动 Zookeeper 服务： [ZooKeeper Document](https://zookeeper.apache.org/doc/current/zookeeperStarted.html)。

## 后端运行

```bash
> git clone https://github.com/sofastack/sofa-dashboard.git
> cd sofa-dashboard
> mvn clean package -DskipTests
> cd sofa-dashboard-backend/sofa-dashboard-web/target/
> java -jar sofa-dashboard-web-1.0.0-SNAPSHOT.jar
```

## 前端运行

sofa-dashboard-front 是 SOFADashboard 的前端代码工程，基于蚂蚁金服开源的前端框架 [antd](https://ant.design/) 开发。

```bash
> cd sofa-dashboard-front
> npm i
> npm start
```


