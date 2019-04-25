# Sampling

Currently,SOFATracer provides two sampling modes. One is the fixed sampling rate based on BitSet. The other is the sampling provided to the user to customize the implementation sampling.The following example shows how to use it.

This example is based on the tracer-sampled-with-springmvc project,Except for `application.properties`, everything else is the same.

## Sampling mode based on fixed sampling rate

### Add sampling related configuration items in application.properties

```properties
#Sampling rate  0~100
com.alipay.sofa.tracer.samplerPercentage=100
#Sampling type name
com.alipay.sofa.tracer.samplerName=PercentageBasedSampler
```

### Verification

* When the sample rate is set to 100, the digest log is printed each time.
* When the sample rate is set to 0, the digest log is not printed.
* Print by probability when the sampling rate is set between 0 and 100.

The result is verified by requesting 10 times.

1、When the sample rate is set to 100, the digest log is printed each time.

Start the project and enter in the browser:http://localhost:8080/springmvc ,And refresh the address 10 times, check the log as follows:

```json
{"time":"2018-11-09 11:54:47.643","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173568757510019269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":68,"current.thread.name":"http-nio-8080-exec-1","baggage":""}
{"time":"2018-11-09 11:54:50.980","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569097710029269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":3,"current.thread.name":"http-nio-8080-exec-2","baggage":""}
{"time":"2018-11-09 11:54:51.542","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569153910049269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":3,"current.thread.name":"http-nio-8080-exec-4","baggage":""}
{"time":"2018-11-09 11:54:52.061","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569205910069269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-6","baggage":""}
{"time":"2018-11-09 11:54:52.560","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569255810089269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-8","baggage":""}
{"time":"2018-11-09 11:54:52.977","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569297610109269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":1,"current.thread.name":"http-nio-8080-exec-10","baggage":""}
{"time":"2018-11-09 11:54:53.389","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569338710129269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-2","baggage":""}
{"time":"2018-11-09 11:54:53.742","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569374110149269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":1,"current.thread.name":"http-nio-8080-exec-4","baggage":""}
{"time":"2018-11-09 11:54:54.142","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569414010169269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-6","baggage":""}
{"time":"2018-11-09 11:54:54.565","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173569456310189269","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-8","baggage":""}
```

2、When the sample rate is set to 0, the digest log is not printed.

Start the project and enter in the browser:http://localhost:8080/springmvc ,And refresh the address 10 times, 
View the ./logs/tracerlog/ directory without the spring-mvc-digest.log log file

3、Print by probability when the sampling rate is set between 0 and 100.

Set the sampling rate to 20

* Request 10 times

```json
{"time":"2018-11-09 12:14:29.466","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173686946410159846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-5","baggage":""}
{"time":"2018-11-09 12:15:21.776","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173692177410319846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-2","baggage":""}
```

* Request 20 times

```json
{"time":"2018-11-09 12:14:29.466","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173686946410159846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-5","baggage":""}
{"time":"2018-11-09 12:15:21.776","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173692177410319846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-2","baggage":""}
{"time":"2018-11-09 12:15:22.439","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173692243810359846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":1,"current.thread.name":"http-nio-8080-exec-6","baggage":""}
{"time":"2018-11-09 12:15:22.817","local.app":"SOFATracerSpringMVC","traceId":"0a0fe8ec154173692281510379846","spanId":"0.1","request.url":"http://localhost:8080/springmvc","method":"GET","result.code":"200","req.size.bytes":-1,"resp.size.bytes":0,"time.cost.milliseconds":2,"current.thread.name":"http-nio-8080-exec-8","baggage":""}
```
> The test results are for reference only

## Custom sampling mode

An sampler interface for sample rate calculation is provided in SOFATracer. If you need to customize your own sampling strategy, you can extend it by implementing this interface. Let's use a simple demo to demonstrate how to use the custom sampling mode.

### Custom sampling rule class

```java
public class CustomOpenRulesSamplerRuler implements Sampler {

    private static final String TYPE = "CustomOpenRulesSamplerRuler";

    @Override
    public SamplingStatus sample(SofaTracerSpan sofaTracerSpan) {
        SamplingStatus samplingStatus = new SamplingStatus();
        Map<String, Object> tags = new HashMap<String, Object>();
        tags.put(SofaTracerConstant.SAMPLER_TYPE_TAG_KEY, TYPE);
        tags = Collections.unmodifiableMap(tags);
        samplingStatus.setTags(tags);

        if (sofaTracerSpan.isServer()) {
            samplingStatus.setSampled(false);
        } else {
            samplingStatus.setSampled(true);
        }
        return samplingStatus;
    }

    @Override
    public String getType() {
        return TYPE;
    }

    @Override
    public void close() {
        // do nothing 
    }
}
```
In the sample method, the user can decide whether to print based on the information provided by the current sofaTracerSpan. This case determines whether to sample by isServer, isServer=true, does not sample, otherwise samples.

### Add sampling related configuration items in application.properties

```properties
com.alipay.sofa.tracer.samplerName.samplerCustomRuleClassName=com.alipay.sofa.tracer.examples.springmvc.sampler.CustomOpenRulesSamplerRuler
```

The relevant test results can be verified by yourself.



