## SOFAMesh介绍

## 概述

SOFAMesh是基于Istio改进和扩展而来的Service Mesh大规模落地实践方案。在继承Istio强大功能和丰富特性的基础上，为满足大规模部署下的性能要求以及应对落地实践中的实际情况，有如下改进：

1. 采用Golang编写的MOSN取代Envoy
2. 合并Mixer到数据平面以解决性能瓶颈
3. 增强Pilot以实现更灵活的服务发现机制
4. 增加对SOFARPC、Dubbo的支持

下图展示了SOFA Mesh和Istio在架构上的不同：

![](resources/sofa-mesh-arch.png)

## 主要组件

### SOFA MOSN

SOFAMesh中，我们采用Golang语言编写了名为MOSN(Modular Observable Smart Netstub)的模块，实现了Envoy的功能，完全兼容Envoy的API，在数据平面中承担Sidecar功能以替代Envoy实现和Istio集成。

![](resources/mosn-sofa-mesh-golang-sidecar.png)

### SOFA Pilot

SOFAMesh中大幅扩展和增强Istio中的Pilot模块：

![](resources/sofa-mesh-pilot.png)

1. 增加SOFA Registry的Adapter，提供超大规模服务注册和发现的解决方案
2. 增加数据同步模块，以实现多个服务注册中心之间的数据交换
3. 增加Open Service Registry API，提供标准化的服务注册功能

MOSN和Pilot配合，将可以提供让传统侵入式框架（如Spring Cloud，Dubbo，SOFA RPC等）和Service Mesh产品可以相互通讯的功能，以便可以平滑的向Service Mesh产品演进和过渡。

