# Quick start for client

## Common Java Project

Add the Maven dependency of the client to the application:

```xml
<dependency>    
    <groupId>com.alipay.sofa.lookout</groupId>
    <artifactId>lookout-client</artifactId>
    <version>${lookout.client.version}</version>
</dependency>
```

Lookout-client relies on the lookout-reg-server module by default (supports reporting metrics data to the lookout server). If you want to use a different type of registry (such as lookout-reg-prometheus), then add the corresponding dependency.

Before starting to use the SOFALookout Client, you must firstly build a global client instance (`com.alipay.lookout.client.DefaultLookoutClient`).

```java
LookoutConfig lookoutConfig = new LookoutConfig();

DefaultLookoutClient client = new DefaultLookoutClient("appName");
// Choose to build the Registry you need to use (if you need multiple registry types, it is recommended to use the same lookoutConfig instance for centralized management).
LookoutRegistry lookoutRegistry = new LookoutRegistry(lookoutConfig);
// Client can add a registry instance (at least one) after the client is created.
client.addRegistry(lookoutRegistry);
// (Optional) Uniformly register the metrics of extended modules for the registry instances that have been added or will be added to the client.
client.registerExtendedMetrics();
```

Then get the Registry instance through the client and use it:

```java
// The registry is a "combination" registry
Registry registry = client.getRegistry();
//demo
Id id = registry.createId("http_requests_total");
Counter counter = registry.counter(id);
counter.inc();
```

For the use of the client, see [Project sample](https://github.com/sofastack/sofa-lookout/tree/master/samples/metrics/client/lookout-client-samples-java).
