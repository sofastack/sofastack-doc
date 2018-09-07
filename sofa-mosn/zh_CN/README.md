# MOSN

![license](https://img.shields.io/badge/license-Apache--2.0-green.svg)

MOSN 是一款采用 Golang 开发的 Service Mesh 数据平面代理，功能和定位类似 Envoy，旨在提供分布式，模块化，可观察，智能化的代理能力。MOSN 支持 Envoy 和 Istio 的 API，可以和 Istio 集成。Sofa Mesh 中，我们使用 MOSN 替代 Envoy。

初始版本由蚂蚁金服和阿里大文娱UC事业部的技术团队携手贡献，期待社区一起来参与 MOSN 项目的后续开发，共建一个开源精品项目。

## 核心能力

+ Istio 集成
    + 集成 Istio 0.8 版本 Pilot V2 API，可基于全动态资源配置运行
+ 核心转发
    + 自包含的网络服务器
    + 支持 TCP 代理
    + 支持 TProxy 模式
+ 多协议
    + 支持 HTTP/1.1，HTTP/2
    + 支持 SOFARPC
    + 支持 Dubbo 协议（开发中）
    + 支持 HSF 协议（开发中）
+ 核心路由
    + 支持 virtual host 路由
    + 支持 headers/url/prefix 路由
    + 支持基于 host metadata 的 subset 路由
    + 支持重试
+ 后端管理&负载均衡
    + 支持连接池
    + 支持熔断
    + 支持后端主动健康检查
    + 支持 random/rr 等负载策略
    + 支持基于 host metadata 的 subset 负载策略
+ 可观察性
    + 观察网络数据
    + 观察协议数据
+ TLS
    + 支持 HTTP/1.1 on TLS
    + 支持 HTTP/2 on TLS
    + 支持 SOFARPC on TLS
+ 进程管理
    + 支持平滑 reload
    + 支持平滑升级
+ 扩展能力
    + 支持自定义私有协议
    + 支持在 TCP IO 层，协议层面加入自定义扩展
    
## 架构设计
* [参考这里](design/README.md)

## 快速开始
* [参考这里](develop/quickstart.md) 

## 文档
* [相关使用文档](reference/README.md)
* [部分开发文档](develop/README.md)

## 社区
* [Issues](https://github.com/alipay/mosn/issues)

## 贡献
+ [代码贡献](./CONTRIBUTING.md) 
+ MOSN 仍处在初级阶段，有很多能力需要补全，很多 bug 需要修复，欢迎所有人提交代码。我们欢迎您参与但不限于如下方面：
   + 核心路由功能点补全
   + http/http2 性能优化
   + Outlier Detection
   + Tracing 支持
   + 流控
   + 性能
   
## 致谢
感谢 Google，IBM，Lyft 创建了 Istio 和 Envoy 体系，并开源这些优秀的项目，使 MOSN 有了非常好的参考，帮助我们能快速落地自己的想法。特此鸣谢！
