# SOFAArk 管控

[SOFAArk](https://www.sofastack.tech/sofa-boot/docs/sofa-ark-readme)  本身提供了多种方式来支持多应用(模块)合并部署 ，包括基于命令行的管控，基于 API 的管控等；SOFAARK 管控是 SOFADashboard 针对 API 的管控的一种实现。通过面向 Zookeeper 进行命令的推送和命令的解析执行。

SOFAArk 管控主要包括以下功能：

* 插件注册：将 ark-biz 插件注册到 SOFADashboard，作为基础数据
* 关联应用：将 ark-biz 插件与宿主应用进行绑定
* 插件详情：通过插件详情页，可以看下当前 ark-biz 插件下所有关联的宿主应用信息，以及宿主应用中的ark-biz 状态信息
* 命令推送：插件详情页，可以针对应用维度、分组维度、IP 维度 推送一些指令，比如 install、uninstall 等等，当这些命令被写入到 Zookeeper 的某个节点上时，所有监听此节点的 宿主应用均会解析此指令，并进行相关的操作

## 插件注册

将 ark-biz 插件注册到 SOFADashboard：

![ark-console.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*xD_oSK6yq4AAAAAAAAAAAABjARQnAQ)

填写插件的基本信息：

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*DsGNQau9wKQAAAAAAAAAAABjARQnAQ)

注册成功之后，模块列表如下：

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*3PnhQ7fqXAwAAAAAAAAAAABjARQnAQ)

## 关联应用

点击模块列表操作菜单栏中的关联应用，可以将一个应用与插件进行绑定：

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*Xh6yQ7-txaIAAAAAAAAAAABjARQnAQ)

点击插件列表后面的 关联应用案例，将插件与应用进行关联，如下：

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*tnc-S7bSXOUAAAAAAAAAAABjARQnAQ)

## 插件详情

点击插件列表后面的 详情 按钮，可以查看当前插件下所有应用信息和应用实例信息。

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*kJ5GTInKWD0AAAAAAAAAAABjARQnAQ)

* 切换版本

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*a1x5Rr5e1eMAAAAAAAAAAABjARQnAQ)

切到之后 因为当前 插件 2.0.0 并没有被安装到宿主应用，因此此时看到的状态信息是空的。

## 命令推送

SOFADashboard 提供三种维度的命令推送

* 基于应用维度，当前应用所有的实例都会监听到此命令变更
* 基于IP 维度，分组维度的单 ip 场景

### 基于 IP 维度

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*TdyoQLZtf2QAAAAAAAAAAABjARQnAQ)

点击 安装按钮，延迟 1~1.5s 之后 界面将会刷新新的状态：

![image.png](https://gw.alipayobjects.com/mdn/sofastack/afts/img/A*yDJEQJHJTDsAAAAAAAAAAABjARQnAQ)

> 基于应用维度此处不再赘述。

