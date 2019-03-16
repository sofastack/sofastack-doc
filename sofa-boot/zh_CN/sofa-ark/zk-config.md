在介绍 [Biz 生命周期](./biz-lifecycle) 时，我们提到了有三种方式控制 Biz 的生命周期，并且介绍了使用客户端 API 实现 Biz 的安装、卸载、激活。在这一章节我们介绍如何使用 SOFAArk 提供的动态配置插件，通过 Zookeeper 下发指令，控制 Biz 的生命周期。

### 引入依赖
SOFAArk 提供了 config-ark-plugin 对接 Zookeeper 配置中心，用于运行时接受配置，达到控制 Biz 生命周期，引入如下依赖：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>config-ark-plugin</artifactId>
    <version>${sofa.ark.version}</version>
</dependency>
```

### 配置 ZK 地址
参考 [SOFAArk 配置](./ark-config.md)，在 SOFAArk 配置文件 `conf/ark/bootstrap.properties` 增加如下配置：

```text
com.alipay.sofa.ark.config.address=zookeeper://ip:port
```

### 配置维度
SOFAArk 启动后，会在 ZK 注册两个节点配置，分别是宿主应用维度和 IP 维度：

+ `sofa-ark/${com.alipay.sofa.ark.master.biz}/`
> 宿主应用维度配置，应用启动时，会拉取该维度配置，控制相关 Biz 的部署；应用重启后，配置不会丢失

+ `sofa-ark/${com.alipay.sofa.ark.master.biz}/ip/`
> IP 维度配置，应用重启后丢失，通常用于运行时控制单台机器的 Biz 行为

通过写这两个节点的配置，可以控制相关机器和应用的 Biz 运行时状态。

### 配置形式
下面介绍配置的形式，动态配置采用状态声明指令，SOFAArk 收到配置后，会根据状态描述解析出具体的指令（包括 install，uninstall, switch），指令格式如下：

`bizName:bizVersion:bizState?k1=v1&k2=v2`

多条指令使用 `;` 隔开，单条指令主要由 biz 名称，biz 版本，biz 预期状态及参数组成。简单记住一点，状态配置是描述指令推送之后，所有非宿主 Biz 的状态；

例如当前 SOFAArk 容器部署了两个应用 A，B，版本均为 1.0，其中 A 应用为宿主应用，因为宿主应用不可卸载，因此不需要考虑宿主应用，可以简单认为当前容器的 Biz 状态声明为：

> `B:1.0:Activated`

如果此时你希望安装 C 应用，版本为 1.0，文件流地址为 urlC，那么推送指令应为：

> `B:1.0:Activated;C:1.0:Activated?bizUrl=urlC`

操作继续，如果你又希望安装安装 B 应用，版本为 2.0，文件流地址为 urlB，且希望 2.0 版本处于激活状态，那么你推送的指令应为：

> `B:1.0:Deactivated;B:2.0:Actaivated?bizUrl=urlB;C:1.0:Activated`

> 解释下为什么是这样配置指令，因为 SOFAArk 只允许应用一个版本处于激活状态，如果存在其他版本，则应处于非激活状态；所以当希望激活 B 应用 2.0 版本时，B 应用 1.0 版本应该声明为非激活状态。另外你可能注意到了 C 应用参数 urlC 不用声明了，原因是目前只有当安装新 Biz 时，才有可能需要配置参数 bizUrl，用于指定 biz 文件流地址，其他场景下，参数的解析没有意义。

操作继续，如果你希望卸载 B 应用 2.0 版本，激活 B 应用 1.0 版本，卸载 C 应用，那么推送的指令声明为：

> `B:1.0:Activated`


从上面的操作描述看，在推送动态配置时，只需要声明期望的 Biz 状态即可，SOFAArk 会根据状态声明推断具体的执行指令，并尽可能保持服务的连续性，以上面最后一步操作为例，SOFAArk 推断的执行指令顺序如下：
+ 执行 switch 指令，激活 B 应用 1.0 版本，钝化 B 应用 2.0 版本，保证服务连续性
+ 执行 uninstall 指令，卸载 B 应用 2.0 版本
+ 执行 uninstall 指令，卸载 C 应用 1.0 版本

### 注意事项
目前只有在安装新 Biz 时才可能使用指令参数 bizUrl，用于指定 Biz 文件流地址。文件流地址字符串是能够直接构建 URL 对象，例如 `file://xxx` 或者 `http://xxx`. 安装新 Biz 时，参数 bizUrl 不是必须的，SOFAArk 提供了扩展点：

```java
@Extensible
public interface BizFileGenerator {
    File createBizFile(String bizName, String bizVersion);
}
```

用于扩展实现，根据 biz 名称和 biz 版本返回 biz 文件。
