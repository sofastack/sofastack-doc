## 如何编译
> 安装 JDK7 及以上，Maven 3.2.5 及以上。

直接下载[代码](https://github.com/sofastack/jarslink)，然后执行如下命令：

```text
cd sofa-jarslink
mvn clean install
```

**注意：不能在子目录（即子模块）下进行编译。因为模块比较多，为了避免一些打包插件的配置重复，如格式化插件、License 插件，因此只在根目录配置。所以如果在子模块执行打包命令时，会有错误提示。**