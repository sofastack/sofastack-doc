# 如何编译

> 安装 JDK7 及以上，Maven 3.2.5 及以上。

直接下载代码，然后执行如下命令：
```
cd sofa-rpc
mvn clean install
```


注意：不能在子目录（即子模块）下进行编译。因为 SOFARPC 模块太多，如果每个子模块都会`install` 和 `deploy`，仓库内会有较多无用记录。
所以在设计 SOFARPC 工程结构的时候，我们决定各个子模块组件是不需要 `install` 和 `deploy` 到仓库里的，我们只会`install` 和 `deploy` 一个`sofa-rpc-all`(`all`) 模块。
