## Consul

使用 Consul 作为服务注册中心只需要在 application.properties 中如下配置即可：

```
com.alipay.sofa.rpc.registry.address=consul://127.0.0.1:8500
```
其中后面的值为 consul 的连接地址，如果需要设置一些其他参数，也可以通过

```
com.alipay.sofa.rpc.registry.address=consul://127.0.0.1:8500?a=1&b=2

```
进行设置