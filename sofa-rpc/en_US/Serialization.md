## Serialization protocol

When using the Bolt communication protocol, SOFARPC can choose different serialization protocols, **which can be hessian2 or protobuf currently**.

By default, SOFARPC uses hessian2 as the serialization protocol. If you need to set the serialization protocol to protobuf, you need to configure the following settings when publishing the service:

```xml
<sofa:service ref="sampleService" interface="com.alipay.sofarpc.demo.SampleService">
    <sofa:binding.bolt>
        <sofa:global-attrs serialize-type="protobuf"/>
    </sofa:binding.bolt>
</sofa:service>
```

That is to add the `<sofa:global-attrs>` tag to the `<sofa:binding.bolt>` tag and set the `serialize-type` attribute to `protobuf`.

Correspondingly, when referencing the service, you also need to change the serialization protocol to protobuf. The setting method is similar to publishing the service:

```xml
<sofa:reference interface="com.alipay.sofarpc.demo.SampleService" id="sampleServiceRef" jvm-first="false">
    <sofa:binding.bolt>
        <sofa:global-attrs serialize-type="protobuf"/>
    </sofa:binding.bolt>
</sofa:reference>
```

Currently, when you use Annotation for service reference, it is not yet supported to set serialization protocol. But this will be supported in future versions. For details, see ISSUE: <https://github.com/sofastack/sofa-boot/issues/278>