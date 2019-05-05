# 泛化调用

泛化调用提供了让客户端在不需要依赖服务端的接口情况下就能够发起调用的能力。目前 SOFARPC 的泛化调用仅支持在 Bolt 通信协议下使用 Hessian2 作为序列化协议，其他的方式并不支持。

## SOFABoot 环境

### 发布服务

发布服务没有什么特殊的,正常发布服务即可.比如

```xml
<!-- generic -->
<bean id="sampleGenericServiceImpl" class="com.alipay.sofa.rpc.samples.generic.SampleGenericServiceImpl"/>
<sofa:service ref="sampleGenericServiceImpl" interface="com.alipay.sofa.rpc.samples.generic.SampleGenericService">
    <sofa:binding.bolt/>
</sofa:service>

```

### 引用服务
```xml
<sofa:reference jvm-first="false" id="sampleGenericServiceReference" interface="com.alipay.sofa.rpc.api.GenericService">
    <sofa:binding.bolt>
        <sofa:global-attrs generic-interface="com.alipay.sofa.rpc.samples.generic.SampleGenericService"/>
    </sofa:binding.bolt>
</sofa:reference>
```

其中,jvm-first根据实际情况,默认可以不写,接口写泛化调用的通用接口,generic-interface中写上自己要调用的接口名称即可.

### 发起调用

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
如上通过 setGeneric 设置该服务为泛化服务，设置服务方的接口名。以 GenericService 作为泛化服务，通过 GenericService 就能够发起泛化调用了。发起调用时，需要传入方法名，方法类型，方法参数。

如果参数或者返回结果在客户端也需要泛化表示。可以通过 GenericObject 来实现。
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
如上就能获得序列化结果。完整的泛化调用方式使用说明如下：
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
 * 服务方提供的接口
 */
interface SampleService {
   String hello(String arg);
   People hello(People people);
   String[] hello(String[] args);
}

/**
 * 客户端
 */
public class ConsumerClass {
   GenericService genericService;

   public void do() {
      // 1. $invoke仅支持方法参数类型在当前应用的 ClassLoader 中存在的情况

      genericService.$invoke("hello", new String[]{ String.class.getName() }, new Object[]{"I'm an arg"});

      
      // 2. $genericInvoke支持方法参数类型在当前应用的 ClassLoader 中不存在的情况。
      // 2.1 构造参数
      GenericObject genericObject = new GenericObject("com.alipay.sofa.rpc.test.generic.bean.People"); // 构造函数中指定全路径类名
      genericObject.putField("name", "Lilei"); // 调用putField，指定field值
      genericObject.putField("age", 15);
      
      // 2.2 进行调用，不指定返回类型，返回结果类型为GenericObject
      Object obj = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject });
      Assert.assertTrue(obj.getClass == GenericObject.class);
      
      // 2.3 进行调用，指定返回类型
      People people = genericService.$genericInvoke("hello", new String[]{"com.alipay.sofa.rpc.test.generic.bean.People"}, new Object[] { genericObject }, People.class);
	  
      // 2.4 进行调用，参数类型是数组类型
      String[] result = (String[]) proxy.$genericInvoke("hello", new String[]{new String[0].getClass().getName()}, new Object[]{ new String[]{"args"} });
   }
}
```
