## 序列化协议

SOFARPC 可以在使用 Bolt 通信协议的情况下，可以选择不同的序列化协议，**目前支持 hessian2 和 protobuf**。

默认的情况下，SOFARPC 使用 hessian2 作为序列化协议，如果需要将序列化协议设置成 protobuf，在发布服务的时候，需要做如下的设置：

```xml
<sofa:service ref="sampleService" interface="com.alipay.sofarpc.demo.SampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs serialize-type="protobuf"/>
    </sofa:binding.bolt>
</sofa:service>
```

即在 `<sofa:binding.bolt>` 标签内增加 `<sofa:global-attrs>` 标签，并且设置 `serialize-type` 属性为 `protobuf`。

对应的，在引用服务的时候，也需要将序列化协议改成 protobuf，设置方式和发布服务的时候类似：

```xml
<sofa:reference interface="com.alipay.sofarpc.demo.SampleService" id="sampleServiceRef" jvm-first="false">
    <sofa:binding.bolt>
        <sofa:global-attrs serialize-type="protobuf"/>
    </sofa:binding.bolt>
</sofa:reference>
```

目前，使用注解的方式尚不能支持设置序列化协议，这个将在后续的版本中支持，详见 ISSUE：<https://github.com/sofastack/sofa-boot/issues/278>