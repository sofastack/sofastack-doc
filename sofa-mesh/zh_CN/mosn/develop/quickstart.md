# 快速开始

本文用于帮助初次接触MOSN项目的开发人员，快速搭建开发环境，完成构建，测试和打包。

## 获取代码

MOSN项目的代码托管在[github](https://github.com/alipay/mosn)，clone时请注意需要放在`GOPATH`下，具体如下：

```bash
# 进入GOPATH下的scr目录
cd $GOPATH/src
# 创建 gitlab.alipay-inc.com/afe 目录
mkdir gitlab.alipay-inc.com
cd gitlab.alipay-inc.com
mkdir afe
cd afe
# clone mosn代码
git clone git@github.com:alipay/mosn.git
```

TBD：具体github地址等开源之后修改

## 导入IDE

使用您喜爱的Golang IDE导入 `$GOPATH/src/gitlab.alipay-inc.com/afe/mosn` 项目。

推荐使用Goland。

## 构建项目

在项目根目录下执行如下命令构建MOSN项目：

```bash
make build
```

## 运行测试

在项目根目录下执行如下命令运行单元测试：

```bash
make unit-test
```

## 打包

在项目根目录下执行如下命令进行打包：

```bash
make rpm
```

