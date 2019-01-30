In SOFABoot, the RPC framework provides some configuration parameters at the application level, and supports application-level parameter configuration, such as port and thread pool, which are bound by Spring Boot's `@ConfigurationProperties`. The binding attribute class is `com.alipay.sofa.rpc.boot.config.SofaBootRpcProperties`, and the configuration prefix is as follows:

```java

static final String PREFIX = "com.alipay.sofa.rpc";
``` 

Then in the `application.properties` file, you can currently configure the following options. Also, you can write the codes based on your own coding habits as well as according to the Spring Boot specification, camel, underline and so on.

```xml
#Standalone fault tolerance
com.alipay.sofa.rpc.aft.regulation.effective # Whether to enable standalone fault tolerance
com.alipay.sofa.rpc.aft.degrade.effective # Whether to enable degradation
com.alipay.sofa.rpc.aft.time.window # Time window
com.alipay.sofa.rpc.aft.least.window.count # Minimum number of calls
com.alipay.sofa.rpc.aft.least.window.exception.rate.multiple # minimum exception rate
com.alipay.sofa.rpc.aft.weight.degrade.rate # Degradation rate
com.alipay.sofa.rpc.aft.weight.recover.rate # Recovery rate
com.alipay.sofa.rpc.aft.degrade.least.weight #Minimum degrading weight
com.alipay.sofa.rpc.aft.degrade.max.ip.count # Maximum number of degraded IPs

# bolt
com.alipay.sofa.rpc.bolt.port # bolt port
com.alipay.sofa.rpc.bolt.thread.pool.core.size # Number of bolt core threads
com.alipay.sofa.rpc.bolt.thread.pool.max.size # Maximum number of bolt threads
com.alipay.sofa.rpc.bolt.thread.pool.queue.size # bolt thread pool queue
com.alipay.sofa.rpc.bolt.accepts.size # Number of connections that server allows client to establish

# rest
com.alipay.sofa.rpc.rest.hostname # rest hostname
com.alipay.sofa.rpc.rest.port # rest port
com.alipay.sofa.rpc.rest.io.thread.size # Number of rest io threads
com.alipay.sofa.rpc.rest.context.path # rest context path
com.alipay.sofa.rpc.rest.thread.pool.core.size # Number of rest core threads
com.alipay.sofa.rpc.rest.thread.pool.max.size # Maximum number of rest threads
com.alipay.sofa.rpc.rest.max.request.size # Maximum rest request size
com.alipay.sofa.rpc.rest.telnet # Whether to allow rest telnet
com.alipay.sofa.rpc.rest.daemon # Whether to hold the port. If true, exit with the main thread exit

# dubbo
com.alipay.sofa.rpc.dubbo.port # dubbo port
com.alipay.sofa.rpc.dubbo.io.thread.size # dubbo io thread size
com.alipay.sofa.rpc.dubbo.thread.pool.max.size #  Maximum number of dubbo business threads
com.alipay.sofa.rpc.dubbo.accepts.size # Number of connections that server allows client to establish
com.alipay.sofa.rpc.dubbo.thread.pool.core.size #Number of dubbo core Threads
com.alipay.sofa.rpc.dubbo.thread.pool.queue.size #Maximum number of dubbo threads

# registry
com.alipay.sofa.rpc.registry.address # Registry center address
com.alipay.sofa.rpc.virtual.host # virtual host
com.alipay.sofa.rpc.bound.host # bind host
com.alipay.sofa.rpc.virtual.port # virtual port
com.alipay.sofa.rpc.enabled.ip.range # IP range in the case of multiple NICs
com.alipay.sofa.rpc.bind.network.interface # Bind NIC

# h2c
com.alipay.sofa.rpc.h2c.port # h2c port
com.alipay.sofa.rpc.h2c.thread.pool.core.size # Number of h2c core threads
com.alipay.sofa.rpc.h2c.thread.pool.max.size # Maximum number of h2c threads
com.alipay.sofa.rpc.h2c.thread.pool.queue.size # h2c queue size
com.alipay.sofa.rpc.h2c.accepts.size # Number of connections that server allows client to establish

#Extension
com.alipay.sofa.rpc.lookout.collect.disable # Whether to disable lookout

#Proxy
com.alipay.sofa.rpc.consumer.repeated.reference.limit # Number of reference proxies that the client is allowed to generate for the same service. It defaults to 3;
```