## ProviderConfig

| Attribute | Name | Default value | Comment |
|:--------------|:-----------------------|:------- ------------|:------------------------------------ ---------------------------------------|
| id | ID | Generated automatically | |
| application | Application object | Empty ApplicationConfig | |
| interfaceId | Service interface (unique identifier) | | Use the actual interface class for both normal calls and return calls.
| uniqueId | Service tag (unique identifier) | | |
| filterRef | Filter configuration example | | List |
| filter | Filter configuration alias | | separated by commas |
| registry | Registry center on the server| | List |
| methods | Method-level configuration | | Map<String, MethodConfig> |
| serialization | Serialization protocol | hessian2 | |
| register | Whether to register | true | It depends on the implementation and may not take effect. |
| subscribe | Whether to subscribe | true | It depends on the implementation and may not take effect. |
| proxy | Proxy type | javassist | As well as JDK dynamic proxy |
| ref | Service interface implementation class | | |
| server | server | | List, and it can be sent to multiple servers at once |
| delay | Time for delaying service publishing| | Service delay |
| weight | Service static weight | | |
| include | Included methods | | |
| exclude | Methods not included | | |
| dynamic | Whether to dynamically register | | |
| priority | Service priority | | |
| bootstrap | Service publishing starter | bolt | |
| executor | Custom thread pool | | |
| timeout | Execution timeout period for server| | |
| concurrents | Concurrent execution request | | Maximum number of parallel executable requests per method under interface. -1 indicates turning off the concurrent filter, and 0 means that filtering is enabled but not limited |
| cacheRef | Result cache implementation class | | |
| mockRef | Mock implementation class | | |
| mock | Whether to enable Mock | | |
| validation | Whether to enable parameter verification (jsr303) |
| compress | Whether to start compression | false | |
| cache | Whether to enable result caching | false | |
| parameters | Extra attributes | | Map<String, String> |
  
## ConsumerConfig

| Attribute | Name | Default value | Comment |
|:---|:-----|:---|-----:|
| id | ID | Generated automatically | |
| application | Application object | Empty ApplicationConfig | |
| interfaceId | Service interface (unique identifier) | | Use the actual interface class for both normal calls and return calls.
| uniqueId | Service tag (Unique identifier) | | |
| filterRef | Filter configuration example | | List |
| filter | Filter configuration alias | | List |
| registry | Registry center on the server | | List |
| methods | Method-level configuration | | Map<String, MethodConfig> |
| serialization | Serialization protocol | hessian2 | |
| register | Whether to register | true | It depends on the implementation and may not take effect. |
| subscribe | Whether to subscribe | true | It depends on the implementation and may not take effect. |
| proxy | proxy type | javassist | As well as JDK dynamic proxy | 
| protocol | Call protocol | bolt | Currently supports bolt, rest, dubbo |
| directUrl | Direct address | | Directly connected to register |
| generic | Whether to generalize calls | false | |
| connectTimeout | Timeout period for connection establishment  | 3000(cover 5000) | |
| disconnectTimeout | Timeout period for disconnection | 5000(cover 10000) | |
| cluster | Cluster mode | failover | |
| connectionHolder | Connection manager implementation | all | |
| loadBalancer | Load balancing algorithm | random |
| lazy | Whether to delay establishing a persistent connection | false |
Sticky | Whether to use sticky connection | false | Skip load balancing algorithm and use previous address |
| inJVM | Whether to switch to JVM call | true | JVM discovers service provider and turns to local |
| check | Whether to check for strong dependencies | false | Startup failure due to no server available |
| Heartbeat | Heartbeat Interval | 30000 | The client sends heartbeat interval to the server. It depends on the implementation and may not take effect. |
| reconnect | Reconnection interval | 10000 | The interval at which the client rebuilds the persistent connection of the port. It depends on the implementation and may not take effect. |
| router | Router configuration alias ​​| | List |
| routerRef | Router configuration example | | List |
| bootstrap | Service reference starter | bolt |
| addressWait | Waiting time for address acquisition | -1 | It depends on the implementation and may not take effect. |
| timeout | Call timeout period | 3000(cover 5000) | |
| retries | Number of retries after failure | 0 | It is related to cluster mode and is read in failover mode. |
| invokeType | Calling type | sync | |
| onReturn | Number of concurrent execution requests | | Maximum number of parallel executable requests per method under interface. -1 indicates turning off concurrent filters, and 0 means that filtering is enabled but not restricted. |
| cacheRef | Result cache implementation class | | |
| mockRef | Mock implementation class | | |
| cache | Whether to enable result caching | false | |
| mock | Whether to enable Mock | | |
| validation | Whether to enable parameter verification | | Based on JSR303 |
| compress | Whether to start compression | false | |
| parameters | Extra attributes | | Map<String, String> |


## MethodConfig

| Attribute | Name | Default value | Comment |
| :--- | :--- | :--- | :---|
| name | Method name | | |
| timeout | Call timeout period | null | |
| retries | Number of retries after failure | null | |
| invokeType | Calling type | null | |
| validation | Whether to enable parameter verification | null | Based on JSR303 |
| onReturn | SofaResponseCallback called on return | null | Used to implement callback |
| concurrent | Number of concurrent execution requests | null | Maximum number of concurrently executeable requests per method under interface. -1 indicates turning off concurrent filters, and 0 means that filtering is enabled but not restricted. |
| validation | Whether to enable parameter verification | null | |
| compress | Whether to start compression | null | |
| parameters | Extra attributes | | Map<String, String> |



## ServerConfig

| id | Id | Default value | Comment |
|:---------------|:----------------------|:------- ----------------|:-------------------------------- --------------------|
| protocol | Protocol | bolt | Currently supports bolt, rest, dubbo |
| host | host | 0.0.0.0 | |
| port | port | 12200 | Default port: bolt:12200, rest:8341, h2c:12300, dubbo:20880|
| contextPath | Context path | / | |
| ioThreads | Number of IO Thread Pools | 0 | It depends on implementation and may not take effect. For example, bolt defaults to cpu*2. 0 means automatic calculation. |
| threadPoolType | Business thread pool type | cached | |
| coreThreads | Size of core business thread pool | 80(override 20) |
| maxThreads | Maximum size of business thread pool | 400(override 200) |
| telnet | Whether to allow telnet | true | It depends on the implementation and may not take effect. For example, bolt does not support telnet. |
| queueType | Business thread pool type | normal | There is also priority queue, etc. |
| queues | Business thread pool queue | 1000(override 0) |
| aliveTime | Business thread pool survival time | 300000(override 60000) | |
| preStartCore | Id | Generated automatically |
| accepts | Maximum number of persistent connections | 100000 | It depends on the implementation and may not take effect. |
| serialization | Serialization protocol | hesisan2 | |
| virtualHost | Virtual host address | | preferred when registering to the registry center |
| virtualPort | virtual host port | | preferred when registering to the registry center |
| epoll | Using | false | It depends on the implementation and may not take effect. |
Daemon | Whether to guard the port | true | If true, exit with the main thread exit; if false, exit actively. |
| adaptivePort | Whether to adjust the port | false | When the port is occupied, +1 automatically to adapt |
| transport | Transport layer implementation | bolt (cover netty4) | It depends on the implementation and may not take effect. |
| autoStart | Whether to automatically start the port | true | |
| stopTimeout | Timeout period (ms) for graceful shutdown | 10000(override 20000) | |
| boundHost | Bound Address | | It defaults to the host value. |
| parameters | Extra attribute | | Map<String, String> |


## RegistryConfig

| Attribute | Name | Default value | Comment |
|:---------------|:---------------------------|:-- --------|:-----------------------|
| protocol | Protocol | zookeeper | Currently supports zookeeper and local |
| address | Registry center Address | | You must select one from the address and index attribute. |
| index | Specify the address of the registry center addressing service | | You must select one from the index and address attribute. |
| register | whether to register service | true | |
| subscribe | Whether to subscribe to service | true | |
| timeout | Timeout period for calling registry center | 10s | |
| connectTimeout | Timeout period for connecting to registry center | 20s | |
| file | local file location used by local protocol | $HOME | |