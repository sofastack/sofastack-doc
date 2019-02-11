## Http 协议基本使用

在 SOFARPC (非SOFABoot 环境)中，当使用Http作为服务端协议的时候，支持Json作为序列化方式，作为一些基础的测试方式使用。

### SOFARPC API 使用


#### 发布服务

```java
 // 只有1个线程 执行
ServerConfig serverConfig = new ServerConfig()
.setStopTimeout(60000)
.setPort(12300)
.setProtocol(RpcConstants.PROTOCOL_TYPE_HTTP)
.setDaemon(true);

// 发布一个服务，每个请求要执行1秒
ProviderConfig<HttpService> providerConfig = new ProviderConfig<HttpService>()
.setInterfaceId(HttpService.class.getName())
.setRef(new HttpServiceImpl())
.setApplication(new ApplicationConfig().setAppName("serverApp"))
.setServer(serverConfig)
.setUniqueId("uuu")
.setRegister(false);
providerConfig.export();
```

#### 服务引用

因为是Http+Json，所以引用方可以直接通过HttpClient进行调用,以下为一段测试代码。

```java
private ObjectMapper mapper = new ObjectMapper();
HttpClient httpclient = HttpClientBuilder.create().build();
 // POST 正常请求
String url = "http://127.0.0.1:12300/com.alipay.sofa.rpc.server.http.HttpService:uuu/object";
HttpPost httpPost = new HttpPost(url);
httpPost.setHeader(RemotingConstants.HEAD_SERIALIZE_TYPE, "json");
ExampleObj obj = new ExampleObj();
obj.setId(1);
obj.setName("xxx");
byte[] bytes = mapper.writeValueAsBytes(obj);
ByteArrayEntity entity = new ByteArrayEntity(bytes,
    ContentType.create("application/json"));

httpPost.setEntity(entity);

HttpResponse httpResponse = httpclient.execute(httpPost);
Assert.assertEquals(200, httpResponse.getStatusLine().getStatusCode());
byte[] data = EntityUtils.toByteArray(httpResponse.getEntity());

ExampleObj result = mapper.readValue(data, ExampleObj.class);

Assert.assertEquals("xxxxx", result.getName());
```