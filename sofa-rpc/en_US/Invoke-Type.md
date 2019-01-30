## Calling type

SOFARPC provides a variety of calling types under the Bolt protocol to meet different scenarios.

### Synchronous

In the synchronous calling type, after the client initiates a call, it will wait for the server to return the result and then perform subsequent operations. This is the default calling type of SOFARPC.

### Asynchronous

In the asynchronous calling type, after the client initiates a call, it will not wait for the result from the server but continue to execute the subsequent business logic. The result returned by the server will be cached by SOFARPC. When the client needs the result, it can call the API to get the result. To set a service to be asynchronous, you can configure the `type` attribute in the corresponding usage mode:

#### XML

In the XML mode, set the `type` attribute of the `<sofa:global-attrs>` tag to `future`:

```xml
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs type="future"/>
    </sofa:binding.bolt>
</sofa:reference>
```

#### Annotation

In Annotation mode, set the `invokeType` attribute of `@SofaReferenceBinding` to `future`:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt", invokeType = "future"))
private SampleService sampleService;
```

#### API in Spring environment

When using the API in Spring environment, you just need to set the `type` attribute of `BoltBindingParam`:

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setType("future");
```

#### API in non-Spring environment

When using the bare API of SOFARPC in a non-Spring environment, you just need to set the `invokeType` attribute of `ConsumerConfig`:

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setInvokeType("future");
```

#### Get the calling result

Currently, there are two ways to get the result of an asynchronous call:

##### Get result directly

You can directly get the result of an asynchronous call in the following way:

```java
String result = (String)SofaResponseFuture.getResponse(0, true);
```

The first parameter is the timeout period for getting the result, and the second parameter indicates whether to clear the result in the thread context.

##### Get JDK native Future

You can get the JDK native Future object in the following way, and then call the Future object from anywhere to get the result:

```java
Future future = SofaResponseFuture.getFuture(true);
```

The first parameter indicates whether to clear the result in the thread context.

### Callback

By using the callback type of the SOFARPC Bolt protocol, SOFARPC doesn't need to wait for the result after initiating a call. After the client receives the result returned from the server, it can automatically call back a callback interface implemented by the user.

To use the callback type of the SOFARPC Bolt protocol, you first need to implement a callback interface, and set the callback interface in the corresponding configuration, and then set the calling type to `callback`.

#### Implement callback interface

SOFARPC provides a callback interface `com.alipay.sofa.rpc.core.invoke.SofaResponseCallback`, which you needs to first implement in order to use the callback type of SOFARPC Bolt protocol. This interface provides three methods:

* `onAppResponse`: SOFARPC will call back this method when the client receives a normal return from the server.
* `onAppException`: SOFARPC will call back this method when the client receives an exception response from the server.
* `onSofaException`: SOFARPC will call back this method when there are some errors in SOFARPC itself, such as routing errors.

#### Set the callback interface

After implementing the callback interface, you need to set the implementation class in the corresponding service reference configuration and set the calling type to callback.

SOFARPC provides two ways to set the callback interface, namely `Callback Class` and `Callback Ref`. Callback Class directly sets the class name of the callback, and SOFARPC generates an instance of the callback class by calling the default constructor of the callback class. While Callback Ref is to directly provide the instance of the callback class.

##### XML

If the service is referenced via XML, set the `type` attribute of the `<sofa:global-attrs>` tag to `callback` and set the `callback-ref` or `callback-class` attribute:

```xml
<bean id="sampleCallback" class="com.example.demo.SampleCallback"/>
<sofa:reference interface="com.example.demo.SampleService" id="sampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs type="callback" callback-ref="sampleCallback"/>
    </sofa:binding.bolt>
</sofa:reference>
```

In the XML mode, the value of `callback-ref` should be the bean name of the callback class.

##### Annotation

If the service is referenced via Annotation, set the `invokeType` attribute of the `@SofaReferenceBinding` annotation to `callback` and set the `callbackClass` or `callbackRef` attribute:

```java
@SofaReference(binding = @SofaReferenceBinding(bindingType = "bolt",
            invokeType = "callback",
            callbackRef = "sampleCallback"))
private SampleService sampleService;
```

In the Annotation mode, the value of the `callbackRef` should be the bean name of the callback class.

##### API in Spring Environment

If you use the API in Spring or Spring Boot environment, set the `type` attribute of `BoltBindingParam` to `callback` and set the `callbackClass` or `callbackRef` attribute:

```java
BoltBindingParam boltBindingParam = new BoltBindingParam();
boltBindingParam.setType("callback");
boltBindingParam.setCallbackClass("com.example.demo.SampleCallback");
```

##### API in non-Spring environment

If you use SOFARPC bare API in non-Spring environment, call `setInvokeType` to set the type to `callback` and call `setOnReturn` to set the callback class:

```java
ConsumerConfig<SampleService> consumerConfig = new ConsumerConfig<SampleService>()
    .setInterfaceId(SampleService.class.getName())
    .setRegistry(registryConfig)
    .setProtocol("bolt")
    .setInvokeType("callback")
    .setOnReturn(new SampleCallback());
```

##### Set the callback interface at call level

In addition to setting the callback interface at service level, you can also set the callback interface at call level, as follows:

```java
RpcInvokeContext.getContext().setResponseCallback(new SampleCallback());
```

### Oneway

When the client does not care about the result returned from the server after sending the request, it can use the oneway calling type. In this type, null is returned immediately after the call is initiated, and the client ignores the result returned from the server.

To use the oneway type, you only need to set the calling type to `oneway`, which is the same as setting the calling type to `future` or `callback`. You can refer to the preceding settings.

Note: Since the oneway calling type returns immediately, all timeout settings are invalid in the case of oneway.