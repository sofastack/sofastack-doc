# 客户端使用

## 1. 创建 Maven 工程

服务端部署完毕后，我们可以新建一个 Maven 工程使用 SOFARegistry 提供的服务。首先新建一个 Maven 工程，然后引入如下依赖：

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>registry-client-all</artifactId>
    <version>${registry.client.version}</version>
</dependency>
```

## 2. 发布数据

```java
// 构建客户端实例
RegistryClientConfig config = DefaultRegistryClientConfigBuilder.start().setRegistryEndpoint("127.0.0.1").setRegistryEndpointPort(9603).build();
DefaultRegistryClient registryClient = new DefaultRegistryClient(config);
registryClient.init();

// 构造发布者注册表
String dataId = "com.alipay.test.demo.service:1.0@DEFAULT";
PublisherRegistration registration = new PublisherRegistration(dataId);

// 将注册表注册进客户端并发布数据
registryClient.register(registration, "10.10.1.1:12200?xx=yy");
```

使用 SOFARegistry 发布数据一共包含三个步骤：

1. 构建客户端实例;
1. 构造发布者注册表;
1. 将注册表注册进客户端并发布数据。


### 2.1 构建客户端实例
构建客户端实例的关键是创建 RegistryClientConfig 对象，创建 RegistryClientConfig 对象时需要指定 RegistryEndpoint 和 RegistryEndpointPort:

* RegistryEndpoint：注册中心任一 Session 节点地址；
* RegistryEndpointPort: Session 节点配置的 session.server.httpServerPort 端口值。


### 2.2 构造发布者注册表
构造发布注册表只需要创建 PublisherRegistration 并指定 dataId，dataId 是发布服务的唯一标识。


### 2.3 发布数据
调用 RegistryClient 的 register 方法可以进行数据发布，该方法需要两个参数，第一个参数是发布注册表，指定了服务的 dataId，第二个参数是数据值，一般是一个字符串类型。

## 3. 订阅数据

```java
// 构建客户端实例
RegistryClientConfig config = DefaultRegistryClientConfigBuilder.start().setRegistryEndpoint("127.0.0.1").setRegistryEndpointPort(9603).build();
DefaultRegistryClient registryClient = new DefaultRegistryClient(config);
registryClient.init();

// 创建 SubscriberDataObserver 
SubscriberDataObserver subscriberDataObserver = new SubscriberDataObserver() {
  	public void handleData(String dataId, UserData userData) {
    		System.out.println("receive data success, dataId: " + dataId + ", data: " + userData);
  	}
};

// 构造订阅者注册表，设置订阅维度，ScopeEnum 共有三种级别 zone, dataCenter, global
String dataId = "com.alipay.test.demo.service:1.0@DEFAULT";
SubscriberRegistration registration = new SubscriberRegistration(dataId, subscriberDataObserver);
registration.setScopeEnum(ScopeEnum.global);

// 将注册表注册进客户端并订阅数据，订阅到的数据会以回调的方式通知 SubscriberDataObserver
registryClient.register(registration);
```

使用 SOFARegistry 发布数据一共包含三个步骤：

1. 构建客户端实例;
2. 创建 SubscriberDataObserver；
3. 构造订阅者注册表；
4. 将注册表注册进客户端并订阅数据。

其中创建客户端实例方式与上文发布数据时创建客户端实例的方法一致。

### 3.1 创建 SubscriberDataObserver
SubscriberDataObserver 是一个回调接口，该接口定义了 handleData 方法，该方法包含两个参数，分别是 dataId 及最终数据，当客户端收到服务端订阅的数据时会调用该方法。在 SOFARegistry 中，服务端返回数据用 UserData 表示，该类包含以下两个方法：

```java
public interface UserData {
    Map<String, List<String>> getZoneData();
    String getLocalZone();
}
```

* getLocalZone: 返回当前zone；
* getZoneData: 返回以 zone 为 key，每个 zone 的数据为 value 的数据。

### 3.2 构造订阅者注册表

构造订阅者注册表需要创建 SubscriberRegistration 对象，创建该对象需要指定 dataId 及 SubscriberDataObserver。

### 3.3 订阅数据

调用 RegistryClient 的 register 方法可以进行数据订阅，该方法包含一个参数，只需传入 SubscriberRegistration 对象即可。

如果先运行发布数据的程序，然后再运行订阅数据的程序，那么我们将在控制端看到如下输出：

```plain
receive data success, dataId: com.alipay.test.demo.service:1.0@DEFAULT, data: DefaultUserData{zoneData={DEFAULT_ZONE=[10.10.1.1:12200?xx=yy]}, localZone='DEFAULT_ZONE'}
```


