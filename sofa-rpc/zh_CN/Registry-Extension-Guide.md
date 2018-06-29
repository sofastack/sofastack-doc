# 《注册中心扩展手册》

如果你要扩展一个注册中心，我们先看下注册中心的抽象类。

```java
package com.alipay.sofa.rpc.registry;

@Extensible(singleton = false)
public abstract class Registry implements Initializable, Destroyable {
    public abstract boolean start();
    public abstract void register(ProviderConfig config);
    public abstract void unRegister(ProviderConfig config);
    public abstract void batchUnRegister(List<ProviderConfig> configs);
    public abstract List<ProviderGroup> subscribe(ConsumerConfig config);
    public abstract void unSubscribe(ConsumerConfig config);
    public abstract void batchUnSubscribe(List<ConsumerConfig> configs);
}
```
可以看到我们需要的主要接口。

 - [ ] 启动注册中心客户端、维持连接
 - [ ] 销毁注册中心客户端、释放资源
 - [ ] 发布服务、缓存发布信息
 - [ ] 取消发布服务、删除缓存
 - [ ] 订阅服务列表、同步或者异步返回数据，有变化接收通知
 - [ ] 取消订阅服务列表、删除缓存

其它
 - [ ] 注册中心节点断连后，不影响本地调用
 - [ ] 和一个注册中心节点断连后，可自己切换到其它注册中心节点
 - [ ] 注册中心节点切换后，自动恢复注册和订阅信息
 - [ ] 注册中心数据缓存到本地文件，就算连不上任何注册中心，服务提供者和服务调用者也能重启并正常调用
  