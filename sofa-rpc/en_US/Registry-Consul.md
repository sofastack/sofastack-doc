## Consul

To use Consul as service registry center, you only need to configure it in `application.properties` as follows:

```
com.alipay.sofa.rpc.registry.address=consul://127.0.0.1:8500
```
The value after `consul:` is the connection address of the consul. If you need to set some other parameters, you can also configure as follows:

```
com.alipay.sofa.rpc.registry.address=consul://127.0.0.1:8500?a=1&b=2

```