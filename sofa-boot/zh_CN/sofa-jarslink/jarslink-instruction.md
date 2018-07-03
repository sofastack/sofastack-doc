Jarslink2.0 支持运行时动态接收指令用于管理 Biz 包生命周期。假设某 Ark 包引入了 Jarslink2.0 插件，在启动该 Ark 包之后，可以使用 telnet 连接协议进行指令交互，端口为 1234。例如 `telnet ip 1234` 即可进入 Jarslink2.0 命令交互界面，在交互界面键入 help，可以获取所有相关指令使用手册。下面我们逐一介绍 Jarslink2.0 指令模式。

+ 安装 Biz 包: 安装指令用于动态部署应用，指令模式 install -b $bizFile 。-b 也可以替换成 -biz，它是 Jarslink2.0 指令标志，所有指令必须带有 -b 或者 -biz。install 命令有且只有一个参数，即 Biz 包 URI 地址，可以是本地文件地址，也可以远程文件链接。例如：install -b file:///Users/qilong.zql/sample-ark-biz.jar 


+ 卸载 Biz 包: 卸载命令用于关闭应用，应用发布的服务及占有的资源都将被销毁。指令模式 uninstall -b -n $bizName -v $bizVersion。卸载命令必须指定 Biz 包名称和版本号，分别通过 -n 和 -v 指定，也可以使用全称 -name 和 -version。Biz 包名称和版本号在打包时就已经确定，详细可参考[应用打包](../)。


+ 切换 Biz 包: 切换指令用于 Biz 包的热更新，保证服务的连续性。Jarslink2.0 允许运行时加载相同名称不同版本的 Biz 包，但是最多只有一个相同名称的 Biz 包对外提供服务。假设运行时有一个 Biz 包已经被加载且对外提供服务，此时需要升级该 Biz 包到某个高版本，可以先执行安装指令加载高版本的 Biz 包。由于已经有一个低版本 Biz 包正对外提供服务，因此高版本 Biz 包处于未激活状态。此时，可以执行切换指令来切换对外提供服务的 Biz 包版本，从而做到应用热更新，并保证应用对外提供服务的连续性。指令模式 switch -b -n $bizName -v $bizVersion，相关参数解释同上。


+ 查询 Biz 包: 查询指令用于查询当前 JVM 安装的 Biz 包及其状态信息。指令模式 check -b -n $bizName -v $bizVersion。其中 Biz 包名称和版本号是可选参数，如果没有指定名称和版本号，将返回所有的 Biz 包信息。如果只指定了名称，没有指定版本号，将返回指定名称所有版本号的 Biz 包信息。