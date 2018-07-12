# 快速开始

本文用于帮助初次接触MOSN项目的开发人员，快速搭建开发环境，完成构建，测试和打包。

## 准备运行环境
+ 如果你使用容器运行MOSN, 请先 [安装docker](https://docs.docker.com/install/)
+ 如果你使用本地机器，请使用类unix环境
+ 安装 go 的编译环境 

## 获取代码

MOSN项目的代码托管在 [github](https://github.com/alipay/mosn)，clone时请注意需要放在`GOPATH`下，具体如下：

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

使用您喜爱的Golang IDE导入 `$GOPATH/src/gitlab.alipay-inc.com/afe/mosn` 项目，推荐Goland。

## 编译代码

在项目根目录下执行如下命令编译mosn的二进制文件：

```bash
make build         //使用docker编译
// or
make build-local   // 使用本地的go编译环境
```

完成后可以在 `bundles/${version}/binary` 目录下找到编译好的二进制文件。


## 运行测试

在项目根目录下执行如下命令运行单元测试：

```bash
bundles/${version}/binary
```

## 打包

+ 在项目根目录下执行如下命令进行打包：

```bash
make rpm
```

+ 在如下的路径中找到打包文件:

```bash
bundles/${version}/rpm
```

## 运行测试

+ 在项目根目录下执行如下命令运行单元测试：

```bash
make unit-test
```

+ 单独运行 mosn 作为proxy转发的事例:

  + 参考 ``mosn/pkg/tests/`` 下的[事例](./RunMosnTests.md)
 
## 如何快速启动一个 mosn 的转发程序

参考 ``samples`` 目录下的示例工程

+ [以sofa proxy为例](./RunMosnSofaProxy.md)
+ [以http proxy为例](./RunMosnHttpProxy.md)
