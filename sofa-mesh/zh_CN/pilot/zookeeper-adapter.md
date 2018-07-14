# Zookeeper Adpater

Zookeeper Adapter 是按照 Istio 注册中心扩展机制开发的一个 Adapter 插件，用于对接所有使用 Zookeeper 作为注册中心的微服务框架。目前已经支持了 SOFARPC，很快将提供对于 Dubbo 的支持。

Zookeeper Adapter 使用 zk 的 watch 机制监听服务注册信息的变化事件，提供了比轮询机制更好的实时性。