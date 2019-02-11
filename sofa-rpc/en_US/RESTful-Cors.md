
## REST Cors

For REST，we provide a  Filter to support cors  now

### SOFARPC API Usage

For users who use SOFARPC API directly，they can add parameters in  ServerConfig

```java
Map<String,String> parameters=new HashMap<String, String>()
parameters.put(RpcConstants.ALLOWED_ORIGINS,"abc.com,cdf.com");
serverConfig.setParameters(parameters);
```

### XML Usage

You can add this configuration to `application.properties`
```xml
com.alipay.sofa.rpc.rest.allowed.origins=a.com,b.com

```
