## Http Protocol

In SOFARPC (Not In SOFABoot/SpringBoot)，when use Http as a protocol of server，we can use Json as the way of serialization，for some basic test scenes.

### SOFARPC API Usage


#### Service Publish

```java
ServerConfig serverConfig = new ServerConfig()
.setStopTimeout(60000)
.setPort(12300)
.setProtocol(RpcConstants.PROTOCOL_TYPE_HTTP)
.setDaemon(true);

ProviderConfig<HttpService> providerConfig = new ProviderConfig<HttpService>()
.setInterfaceId(HttpService.class.getName())
.setRef(new HttpServiceImpl())
.setApplication(new ApplicationConfig().setAppName("serverApp"))
.setServer(serverConfig)
.setUniqueId("uuu")
.setRegister(false);
providerConfig.export();
```

#### Service Consume

Because of the Http+Json，So users can use HttpClient to start a normal call, this is a piece of code in test.

```java
private ObjectMapper mapper = new ObjectMapper();
HttpClient httpclient = HttpClientBuilder.create().build();
 // POST normal request
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

You will get some result.