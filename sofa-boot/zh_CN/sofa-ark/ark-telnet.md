SOFAArk 容器提供了一个简单的 telnet 服务端小工具，用于运行时查看容器状态，目前支持查看 Plugin 和 Biz 相关信息。

### 使用方式
使用 telnet 连接服务端，端口号为 1234， 例如：

> telnet localhost 1234

进入交互界面:

```text
➜ telnet localhost 1234
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
sofa-ark>

sofa-ark>

sofa-ark>
```

目前支持查看 Plugin 和 Biz 相关信息，相关命令可参考提示信息：

```text
sofa-ark>h
Plugin Command Tips:
  USAGE: plugin [option...] [pluginName...]
  SAMPLE: plugin -m plugin-A plugin-B
  -h  Shows the help message.
  -a  Shows all plugin name.
  -m  Shows the meta info of specified pluginName.
  -s  Shows the service info of specified pluginName.
  -d  Shows the detail info of specified pluginName.
Biz Command Tips:
  USAGE: biz [option...] [arguments...]
  SAMPLE: biz -m bizIdentityA bizIdentityB.
  -h  Shows the help message.
  -a  Shows all biz.
  -m  Shows the meta info of specified bizIdentity.
  -s  Shows the service info of specified bizIdentity.
  -d  Shows the detail info of specified bizIdentity.
  -i  Install biz of specified bizIdentity or bizUrl.
  -u  Uninstall biz of specified bizIdentity.
  -o  Switch biz of specified bizIdentity.

sofa-ark>
```

### Plugin 命令
如提示信息所说，plugin 支持查看插件相关信息，包括类(资源)导入导出配置、插件打包配置等。例如：

```text
sofa-ark>plugin -md runtime-sofa-boot-plugin
PluginName:       runtime-sofa-boot-plugin
Version:          3.1.3
Priority:         1500
Activator:        com.alipay.sofa.runtime.integration.activator.SofaRuntimeActivator
Export Packages:  com.alipay.sofa.runtime.api.*,com.alipay.sofa.runtime.client.*,com.alipay.sofa.runtime.component.*,com.alipay.sofa.runtime.constants.*,com.alipay.sofa.runtime.integration.*,com.alipay.sofa.runtime.model.*,com.alipay.sofa.runtime.service.component,com.alipay.sofa.runtime.service.helper,com.alipay.sofa.runtime.spi.client,com.alipay.sofa.runtime.spi.component,com.alipay.sofa.runtime.spi.health,com.alipay.sofa.runtime.spi.log,com.alipay.sofa.runtime.spi.binding,com.alipay.sofa.runtime.spi.util,org.aopalliance.aop,org.aopalliance.intercept
Import Packages:  \
Export Classes:   com.alipay.sofa.runtime.service.binding.JvmBinding,com.alipay.sofa.runtime.SofaFramework,com.alipay.sofa.runtime.SofaRuntimeProperties,com.alipay.sofa.runtime.service.binding.JvmBindingParam,com.alipay.sofa.runtime.spi.service.ServiceProxy
Import Classes:   \
Export Resources: \
Import Resources: \
GroupId:     com.alipay.sofa
ArtifactId:  runtime-sofa-boot-plugin
Version:     3.1.3
URL:         jar:file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/
ClassLoader: com.alipay.sofa.ark.container.service.classloader.PluginClassLoader@420a63fb
ClassPath:   file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/log4j-api-2.11.1.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/aopalliance-1.0.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/log4j-to-slf4j-2.11.1.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/sofa-common-tools-1.0.18.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/log-sofa-boot-starter-1.0.18.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/runtime-sofa-boot-plugin-3.1.3.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/runtime-sofa-boot-starter-3.1.3.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/slf4j-api-1.7.25.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/hessian-3.3.6.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/javax.annotation-api-1.3.2.jar!/,file:/Users/qilong.zql/.m2/repository/com/alipay/sofa/runtime-sofa-boot-plugin/3.1.3/runtime-sofa-boot-plugin-3.1.3.jar!/lib/infra-sofa-boot-starter-3.1.3.jar!/

sofa-ark>
```

其他命令可以查看提示信息，暂时不支持 `-s` 选项查看服务。

### Biz 命令
biz 命令支持查看 biz 相关信息，也支持安装、卸载、激活等 biz 操作指令。例如

```text
sofa-ark>biz -m mvc-two:0.0.1-SNAPSHOT
BizName:                  mvc-two
Version:                  0.0.1-SNAPSHOT
Priority:                 100
MainClass:                com.example.mvctwo.MvcTwoApplication
WebContextPath:           another
Deny Import Packages:     \
Deny Import Classes:      \
Deny Import Resources:    \

sofa-ark>
```

其他命令可以查看提示信息，暂时不支持 `-s` 选项查看服务。需要注意的是，Telnet 服务端只是 SOFAArk 提供的方便运行时查看信息工具，不推荐直接通过 telnet 指令在线上执行 Biz 操作指令，推荐使用 API 或者动态配置方式。