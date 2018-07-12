## 模型
### 应用
Jarslink 管理多应用生命周期，在运行时动态部署时，通常是将一个 Jar 文件实体转成一个抽象模型 Biz.
+ Biz, 应用在运行时的抽象模型

### 指令
Jarslink 目前支持 telnet 协议方式，接受输入的指令。后面也会支持提供 API 的方式执行指令操作。可供接受的指令类型：
+ InstallCommand，安装应用
+ UninstallCommand，卸载应用
+ CheckCommand，查询应用状态
+ SwitchCommand，切换应用状态

### 服务
Jarslink 插件扩展了 SOFAArk 容器的 `BizDeployer` 和 `CommandProvider` 服务，并引用了 SOFAArk 容器暴露的 `BizManagerService` 和 `BizFactoryService` 服务。
+ BizDeployer，SOFAArk 容器提供的应用部署扩展点，用于控制 Ark 包中的 Biz 启动。Jarslink 向 SOFAArk 容器注册了自身的实现。
+ CommandProvider，SOFAArk 容器提供的命令处理扩展点，用于处理 SOFAArk 容器通过 telnet 连接接受的指令。
+ BizManagerService，SOFAArk 容器暴露的 Biz 管理器，用于注册、注销等操作。
+ BizFactoryService，SOFAArk 容器暴露的 Biz 生成器，用于将静态 Biz 包文件抽象成运行时 Biz 模型。
