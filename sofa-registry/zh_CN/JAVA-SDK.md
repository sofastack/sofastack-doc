# JAVA SDK
## 1. Maven 坐标

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>registry-client-all</artifactId>
    <version>${registry.client.version}</version>
</dependency>
```

## 2. 创建SOFARegistry客户端实例
构建 SOFARegistry 客户端实例的关键代码如下：

```java
RegistryClientConfig config =  DefaultRegistryClientConfigBuilder.start().setRegistryEndpoint("127.0.0.1").setRegistryEndpointPort(9603).build();
DefaultRegistryClient registryClient = new DefaultRegistryClient(config);
registryClient.init();
```

其中注册中心相关的属性通过 DefaultRegistryClientConfigBuilder 构建指定，该类包含以下关键属性：

```java
public class DefaultRegistryClientConfigBuilder {
    private String  instanceId;
    private String  zone                      = DEFAULT_ZONE;
    private String  registryEndpoint;
    private int     registryEndpointPort      = 9603;
    private String  dataCenter                = DEFAULT_DATA_CENTER;
    private String  appName;
    private int     connectTimeout            = 3000;
    private int     socketTimeout             = 3000;
    private int     invokeTimeout             = 1000;
    private int     recheckInterval           = 500;
    private int     observerThreadCoreSize    = 5;
    private int     observerThreadMaxSize     = 10;
    private int     observerThreadQueueLength = 1000;
    private int     syncConfigRetryInterval   = 30000;
}
```

| 属性名 | 属性类型 | 描述 |
| --- | --- | --- |
| instanceId | String | 实例ID，发布订阅时需要使用相同值，数据唯一标识由dataId+group+instanceId组成，默认值 DEFAULT_INSTANCE_ID。 |
| zone | String | 单元化所属 zone，默认值 DEFAULT_ZONE。 |
| registryEndpoint | String | 服务端任一 Session 节点地址。 |
| registryEndpointPort | int | Session 节点配置的 session.server.httpServerPort 端口值，默认值 9603。 |
| dataCenter | String | 数据中心，默认值 DefaultDataCenter。 |
| appName | String | 应用名。 |
| connectTimeout | int | 与服务端建立连接超时时间，默认值 3000ms。 |
| socketTimeout | int | 访问服务端 REST 接口超时时间，默认值 3000ms。 |
| invokeTimeout | int | 调用服务端服务超时时间，默认值 1000ms。 |
| recheckInterval | int | 检测任务队列间隔时间，默认值 500ms。 |
| observerThreadCoreSize | int | 处理服务端推送数据线程池核心线程大小，默认值 5。 |
| observerThreadMaxSize | int | 处理服务端推送数据线程池最大线程大小，默认值 10。 |
| observerThreadQueueLength | int | 处理服务端推送数据线程池队列大小，默认值 1000。 |
| syncConfigRetryInterval | int | 同步服务端列表间隔时间，默认值 30000ms。 |

## 3. 发布数据
发布数据的关键代码如下：

```java
// 构造发布者注册表
PublisherRegistration registration = new PublisherRegistration("com.alipay.test.demo.service:1.0@DEFAULT");
registration.setGroup("TEST_GROUP");
registration.setAppName("TEST_APP");

// 将注册表注册进客户端并发布数据
Publisher publisher = registryClient.register(registration, "10.10.1.1:12200?xx=yy");

// 如需覆盖上次发布的数据可以使用发布者模型重新发布数据
publisher.republish("10.10.1.1:12200?xx=zz");
```

发布数据的关键是构造 PublisherRegistration，该类包含三个属性：

| 属性名 | 属性类型 | 描述 |
| --- | --- | --- |
| dataId | String | 数据ID，发布订阅时需要使用相同值，数据唯一标识由 dataId + group + instanceId 组成。 |
| group | String | 数据分组，发布订阅时需要使用相同值，数据唯一标识由 dataId + group + instanceId 组成，默认值 DEFAULT_GROUP。 |
| appName | String | 应用 appName。 |


## 4. 订阅数据
订阅数据的关键代码如下：

```java
// 创建 SubscriberDataObserver 
SubscriberDataObserver subscriberDataObserver = new SubscriberDataObserver() {
    @Override
    public void handleData(String dataId, UserData userData) {
        System.out.println("receive data success, dataId: " + dataId + ", data: " + userData);
    }
};

// 构造订阅者注册表，设置订阅维度，ScopeEnum 共有三种级别 zone, dataCenter, global
String dataId = "com.alipay.test.demo.service:1.0@DEFAULT";
SubscriberRegistration registration = new SubscriberRegistration(dataId, subscriberDataObserver);
registration.setGroup("TEST_GROUP");
registration.setAppName("TEST_APP");
registration.setScopeEnum(ScopeEnum.global);

// 将注册表注册进客户端并订阅数据，订阅到的数据会以回调的方式通知 SubscriberDataObserver
Subscriber subscriber = registryClient.register(registration);
```

订阅数据需要创建两个关键类，分别是 SubscriberDataObserver 和 SubscriberRegistration，前者是收到服务端数据后的回调接口，后者是订阅者注册表，用于向服务端注册订阅信息。

### 4.1 SubscriberDataObserver
SubscriberDataObserver 仅包含一个方法：

```java
void handleData(String dataId, UserData data);
```

参数含义

| 名称 | 类型 | 描述 |
| :--- | :--- | --- |
| dataId | String | 数据ID |
| data | UserData | 数据内容 |

UserData 包装了数据内容，该类包含以下方法：

```java
public interface UserData {
    Map<String, List<String>> getZoneData();
    String getLocalZone();
}
```

* getLocalZone: 返回当前zone；
* getZoneData: 返回以 zone 为 key，每个 zone 的数据为 value 的数据。

### 4.2 SubscriberRegistration
SubscriberRegistration 包含以下四个属性：

| 属性名 | 属性类型 | 描述 |
| --- | --- | --- |
| dataId | String | 数据ID，发布订阅时需要使用相同值，数据唯一标识由 dataId + group + instanceId 组成。 |
| group | String | 数据分组，发布订阅时需要使用相同值，数据唯一标识由 dataId + group + instanceId 组成，默认值 DEFAULT_GROUP。 |
| appName | String | 应用appName |
| scopeEnum | ScopeEnum | 枚举值，表示 Zone、DataCenter、Global 三种维度之一。 |
| subscriberDataObserver | SubscriberDataObserver | 服务端数据后的回调接口。 |

## 5. 注销发布及订阅
取消发布订阅的接口如下：

```java
int unregister(String dataId, String group, RegistryType registryType);
```

请求参数

| 参数名 | 参数类型 | 描述 |
| --- | --- | --- |
| dataId | String | 数据ID。 |
| group | String | 数据分组。 |
| registryType | RegistryType | 枚举值，表示 PUBLISHER、SUBSCRIBER、CONFIGURATOR 三者维度之一。 |

方法返回值为 int 类型，表示注销的发布者及订阅者个数。
