# Generic call

Generic calls provide the ability for clients to initiate calls without having to rely on the server`s interface. Currently, the generic call of SOFARPC only supports using Hessian2 as the serialization protocol under the Bolt communication protocol.

## SOFABoot environment

### Publish Service

There is nothing special about publishing a service. Just publish the service normally, for example:

```xml
<!-- generic -->
<bean id="sampleGenericServiceImpl" class="com.alipay.sofa.rpc.samples.generic.SampleGenericServiceImpl"/>
<sofa:service ref="sampleGenericServiceImpl" interface="com.alipay.sofa.rpc.samples.generic.SampleGenericService">
    <sofa:binding.bolt/>
</sofa:service>

```

### Reference Service
```xml
<sofa:reference jvm-first="false" id="sampleGenericServiceReference" interface="com.alipay.sofa.rpc.api.GenericService">
    <sofa:binding.bolt>
        <sofa:global-attrs generic-interface="com.alipay.sofa.rpc.samples.generic.SampleGenericService"/>
    </sofa:binding.bolt>
</sofa:reference>
```

The `jvm-first` can be left empty according to the actual situation. The interface should be the general interface of generic call. As for the `generic-interface`, you can just write in the name of the interface to be called.

### Initiate a call

````java
 GenericService sampleGenericServiceReference = (GenericService) applicationContext
            .getBean("sampleGenericServiceReference");
 GenericObject genericResult = (GenericObject) sampleGenericServiceReference.$genericInvoke("sayGeneric",
            new String[] { "com.alipay.sofa.rpc.samples.generic.SampleGenericParamModel" },
            new Object[] { genericObject });
````

## RPC API 



```java
ConsumerConfig<GenericService> consumerConfig = new ConsumerConfig<GenericService>()
           .setInterfaceId("com.alipay.sofa.rpc.quickstart.HelloService")
           .setGeneric(true);
GenericService testService = consumerConfig.refer();

String result = (String) testService.$invoke("sayHello", new String[] { "java.lang.String" },new Object[] { "1111" });
```
You can set the service as a generic service and set the interface name of the server by `setGeneric` as above. `GenericService` is used as a generic service, and GenericService can initiate generic calls. You need to pass in the method name, method type, and method parameters when invoking a call.

If the parameter or return result is also required to be generalized on the client side, you can achieve this with `GenericObject`.
```java
GenericObject genericObject = new GenericObject("com.alipay.sofa.rpc.invoke.generic.TestObj");
              
genericObject.putField("str", "xxxx");
genericObject.putField("num", 222);

GenericObject result = (GenericObject) testService.$genericInvoke("echoObj",
                    new String[] { "com.alipay.sofa.rpc.invoke.generic.TestObj" },
                    new Object[] { genericObject });

String str = result.getField("str");
String num = result.getField("num");
```
The serialization result can be obtained as above. The instructions for the complete generalization call are as follows:
```java
/**
* Java Bean
*/
public class People {
    private String name;
    private int    age;
    
    // getters and setters
}


/**
 * Interface provided by the service provider
 */
interface SampleService {
   String hello(String arg);
   People hello(People people);
   String[] hello(String[] args);
}

/**
 * Client
 */
public class ConsumerClass {
   GenericService genericService;

   public void do() {
      // 1. $invoke only supports the scenario where the method parameter types exsit in the current application`s ClassLoader.
      genericService.$invoke("hello", new String[]{ String.class.getName() }, "I'm an arg");
      
      // 2. $genericInvoke supports the scenario where the method parameter types do not exist in the current application`s  ClassLoader.
      // 2.1 Construct parameters
      GenericObject genericObject = new GenericObject("com.alipay.sofa.rpc.test.generic.bean.People"); // Specify the full path class name in the constructor.
      genericObject.putField("name", "Lilei"); // Call putField to specify the field value.
      genericObject.putField("age", 15);
      
      // 2.2 make a call without a return type specified,  then the returned result type is GenericObject.
      Object obj = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject });
      Assert.assertTrue(obj.getClass == GenericObject.class);
      
      // 2.3 make a call with the return type specified.
      People people = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject }, People.class);
	  
      // 2.4 make a call, and the parameter type is array.
      String[] result = (String[]) proxy.$genericInvoke("hello", new String[]{new String[0].getClass().getName()}, new Object[]{ new String[]{"args"} });
   }
}
```