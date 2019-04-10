# 快速开始

目前在 1.0 版本版本中，结构上采取的是前后端分离的方式，前端基于 Ant Design Pro 演进而来，后端采用 SOFABoot v3.0.0 框架。

## 环境准备

### 数据库初始化

> Mysql 版本：5.6+

SOFAArk 管控需要依赖 MySQL 进行资源数据存储，工程目录下有一个 SofaDashboardDB.sql 脚本文件，可以通过执行这个脚本文件进行数据库表的初始化。

### Zookeeper 

> ZooKeeper 3.4.x and ZooKeeper 3.5.x

SOFADashboard 中的服务治理、SOFAArk 管控依赖于 Zookeeper，需要本地启动 Zookeeper 服务： [ZooKeeper Document](https://zookeeper.apache.org/doc/current/zookeeperStarted.html)。

## 后端运行

```bash
> git clone https://github.com/alipay/sofa-dashboard.git
> cd sofa-dashboard
> mvn clean package
> cd sofa-dashboard-web/target/
> java -jar sofa-dashboard-web-1.0.0-SNAPSHOT.jar
```

## 前端运行

sofa-dashboard-front 是 SOFADashboard 的前端代码工程，基于蚂蚁金服开源的前端框架 [antd](https://ant.design/) 开发。

```bash
> git clone https://github.com/alipay/sofa-dashboard-front.git
> cd sofa-dashboard-front
> npm i
> npm start
```


