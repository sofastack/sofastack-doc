# 公用模型

## 消息
内部全部使用 `SofaRequest` 和 `SofaResponse` 进行传递。

如果需要转换为其它协议，那么在真正调用和收到请求的时候，转换为实际要传输的对象。

可以对 `SofaRequest` 和 `SofaResponse` 进行写操作的模块：
- Invoker
- Filter
- ServerHandler
- Serialization 

对消息体是只读的模块：
- Cluster
- Router
- LoadBalance

## 日志

日志的初始化也是基于扩展机制。虽然是扩展，但是由于日志的加载应该是最早的，所以在 `rpc-config.json` 里有一个单独的 Key。

```json
{
  // 日志实现，日志早于配置加载，所以不能适应Extension机制
  "logger.impl": "com.alipay.sofa.rpc.log.MiddlewareLoggerImpl"
}
```

## 配置项

### 使用者的 RPC 配置
用户的配置，例如端口配置（虽然已经开放对象中设置端口的字段，但是sofa默认是从配置文件里取的），线程池大小配置等。
- 通过 `SofaConfigs` 加载配置，调用 ExternalConfigLoader 读取外部属性。 
- 通过 `SofaConfigs` 提供的 API 进行获取。
- 所有内部配置的Key都在 `SofaOptions` 类
- 优先级： `System.property` > `sofa-config.properties`(每个应用一个) > `rpc-config.properties`

### RPC 框架配置
框架自身的配置，例如默认序列化，默认超时等。 未来要一个ClassLoader一个。
- 通过 `RpcConfigs` 加载配置文件。 
- 通过 `RpcConfigs` 其提供的API进行获取和监听数据变化
- 所有内部配置的Key都在 `RpcOptions` 类
- 优先级： `System.property` > `custom rpc-config.json`（可能存在多个自定义，会排序） > `rpc-config-default.json`

### 常量
- 全局的基本常量在 `RpcConstants` 中。例如：
    - 调用方式 sync oneway 
    - 协议 bolt/grpc、 序列化 hessian/java/protobuf   
    - 上下文的key
    - 等等。
- 如果扩展实现自身的常量，请自行维护。
	- 例如 BOLT 协议的常量。
		- SERIALIZE_CODE_HESSIAN = 1
	    - PROTOCOL_TR = 13
 	- 例如 DSR 配置中心相关的常量。
		- _WEIGHT、_CONNECTTIMEOUT 这种 配置中心特有的key

### 地址
- 地址信息放到 `ProviderInfo` 类中
- `ProviderInfo` 的值主要分为三部分
	- 字段，一般是一些必须项目。 例如IP，端口，状态等；
	- 静态字段：例如应用名；
	- 动态字段：例如预热权重等；
- 字段枚举维护在 `ProviderInfoAttrs` 类中。