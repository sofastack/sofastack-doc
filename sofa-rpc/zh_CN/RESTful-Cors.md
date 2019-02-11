
## REST 跨域

对于 REST，我们内置了一个跨域 Filter 的支持，目前


### SOFARPC API 使用

对于使用 SOFARPC API 的用户，可以在 ServerConfig 中添加一个参数表明即可

```java
Map<String,String> parameters=new HashMap<String, String>()
parameters.put(RpcConstants.ALLOWED_ORIGINS,"abc.com,cdf.com");
serverConfig.setParameters(parameters);
```

### XML 方式使用

直接通过配置
```xml
com.alipay.sofa.rpc.rest.allowed.origins=a.com,b.com

```

即可
