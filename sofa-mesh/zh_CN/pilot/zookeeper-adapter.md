# Zookeeper Adpater 介绍

Zookeeper adapter 是按照 istio 注册中心扩展机制开发的一个 adapter 插件，用于对接所有使用 zookeeper 作为注册中心的微服务框架。目前已经支持了 sofa rpc，很快将提供对于 dubbo 的支持。

Zookeeper adapter 使用 zk 的 watch 机制监听服务注册信息的变化事件，提供了比轮训机制更好的实时性。