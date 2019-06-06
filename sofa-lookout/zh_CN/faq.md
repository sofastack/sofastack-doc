# 与 Prometheus的差异
答：主要包括:（1）Lookout metrics server 支持适配更多的协议接入；（2）聚焦在围绕 ES 生态提供易使用和运维的最佳实践；（3）支持计算能力下推；（4）除了 Metrics 后期会有 tracing，eventing等方案；
（5）对聚合函数和 REST API 都做了兼容性的扩展和增强；（6）支持分布式集群部署具备高可用能力。

# Lookout客户端会提供多语言(c,go,python...)支持吗
答：暂时不会。因为 Lookout Gateway已经支持很多主流协议的数据上报，同时也支持自定义扩展。 如果非Java技术栈，我们推荐大家使用其他开源主流的sdk库，比如: Prometheus sdk，Metricbeat等

# "In order to improve query performance, you need to add tag filtering! realQuery:jvm.classes.loaded"
答：只输入metric name查询会影响查询性能，所以我们强制每个查询至少有一个 tag（label）的筛选能力，比如:"jvm.classes.loaded{app="lookout-gateway"}"。
