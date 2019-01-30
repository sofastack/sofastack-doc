# Common models

## Messages
All messages are internally passed through `SofaRequest` and `SofaResponse`.

To convert to other protocols, you need to transform the messages to the objects that are to be actually transferred when calling and receiving requests.

The modules that can write `SofaRequest` and `SofaResponse` are as follows:

- Invoker
- Filter
- ServerHandler
- Serialization

The modules that can only read message bodies are as follows:

- Cluster
- Router
- LoadBalance

## Logs

The log initialization is based on the extension mechanism. Since the log loading should be done earliest, there is a separate key in `rpc-config.json`.

```json
{
  / / Log implementation is done earlier than configuration loading, so it cannot adapt to the extension mechanism
  "logger.impl": "com.alipay.sofa.rpc.log.MiddlewareLoggerImpl"
}
```

## Configuration items

### RPC configuration for users
The user configuration includes port configuration (although the fields for setting port in the objects have been opened, SOFA gets those fields from the configuration file by default), thread pool size configuration, and other configurations.

- Load the configuration via `SofaConfigs` and call `ExternalConfigLoader` to read external properties.
- Get the configuration through the API provided by `SofaConfigs`.
- All the internally configured keys are in the `SofaOptions` class.
- Priority: `System.property` > `sofa-config.properties` (one for each application) > `rpc-config.properties`.

### RPC framework configuration
The configuration of the framework itself, such as default serialization and default timeout. In the future, SOFARPC may support the configuration for multiple ClassLoaders.

- Load the configuration file via `RpcConfigs`.
- Get and listen to data changes via the API provided by `RpcConfigs`
- All internally configured keys are in the `RpcOptions` class
- Priority: `System.property` > `custom rpc-config.json` (There may be multiple custom configuration files which are sorted in certain order) > `rpc-config-default.json`.

### Constants
- The global basic constants are in `RpcConstants`. For example:
 
	- Calling method (sync/oneway)
	- Protocol (bolt/grpc/rest); serialization (hessian/java/protobuf)
	- Context key

- If the extension implements its own constants, you need to maintain the constants yourself. For example:
	- The constants of BOLT protocol
		- SERIALIZE_CODE_HESSIAN = 1
		- PROTOCOL_TR = 13
	- The constants related with DSR Configuration Center
		- The keys specific for DSR Configuration Center, such as _WEIGHT and _CONNECTTIMEOUT

### Address
- The address information of provider is placed in the `ProviderInfo` class.
- The value of `ProviderInfo` is mainly divided into three parts:
	- Fields, which are generally required items, such as IP, port and status.
	- Static fields, such as application name.
	- Dynamic fields, such as warm-up weight.
- Field enumerations are maintained in the `ProviderInfoAttrs` class.