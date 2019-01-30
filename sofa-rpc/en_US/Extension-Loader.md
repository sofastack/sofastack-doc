# ExtensionLoader

To ensure that all steps of SOFARPC have sufficient scalability, SOFARPC defines a very flexible extension mechanism in which all extension implementations are equal.

This mechanism is very useful for both SOFARPC developers and users. SOFARPC abstracts itself into multiple modules which have no explicit dependencies on each other and interact via SPI.

This extension mechanism abstracts the interaction method of SPI. If you have read the documents about Filter and Router, you may have such experience.

The following sections introduce how to extend through the SPI interaction method.

SOFARPC provides the capabilities of ExtensionLoader.


## Design extension points

SOFARPC defines an annotation `@Extensible`, which is on the interface or abstract class to identify that the class is an extension point. Namely, it informs SOFARPC that the class is extensible and SOFARPC needs to find the implementation of the extension point. In addition, the annotation defines the file name of the implementation class and whether the class is a singleton.

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE })
public @interface Extensible {

    /**
     * Specify the name of the custom extension file, which is the full class name by default
     *
     * @return Custom extension file name
     */
    String file() default "";

    /**
     * Whether the extension class uses a singleton, yes by default
     *
     * @return Whether to use a singleton
     */
    boolean singleton() default true;

    /**
     * Whether the extension class needs to be encoded, not by default
     *
     * @return Whether to encode
     */
    boolean coded() default false;
}
```

SOFARPC also defines the `@Extension` annotation, which indicates an extension implementation class. It also defines the name that the extension point uses when looking for an extension implementation in the file.

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE })
public @interface Extension {
    /**
     * Extension point name
     *
     * @return Extension point name
     */
    String value();

    /**
     * Extension point coding, not required by default; it is required when the interface needs to be encoded
     *
     * @return Extension point encoding
     * @see Extensible#coded()
     */
    byte code() default -1;

    /**
     * Priority sorting, not required by default, the larger number, the higher priority
     *
     * @return Sort
     */
    int order() default 0;

    /**
     * Whether to override other extensions with low {@link #order()}
     *
     * @return Whether to override other low-order extensions
     * @since 5.2.0
     */
    boolean override() default false;

    /**
     * Exclude other extensions, namely exclude other extensions with low {@link #order()} 
     *
     * @return Excludes other extensions
     * @since 5.2.0
     */
    String[] rejection() default {};
}
```

## Add extension point

1. Define extension points.

```java
    @Extensible
    public interface Person {
    
        void getName();
    }
```

2. Define the extension implementation.

```java
    @Extension("A")
    public class PersonA implements Person{
        @Override
        public void getName() {
            System.out.println("li wei");
        }
    }
```

3. Compile the extension description file `META-INF/services/sofa-rpc/com.alipay.sofa.rpc.extension.Person`. The file content is as follows:

```plain
A=com.alipay.sofa.rpc.extension.PersonA
```

4. Load the extension points and get the extension implementation class.

```java
Person person = ExtensionLoaderFactory.getExtensionLoader(Person.class).getExtension("A");
```

## Existing extension points

If you want to extend the function of each built-in extension point in SOFARPC, you can directly implement the existing extensions and configure the extended file.

The existing extension points are as follows:

Interface name|Definition|Remarks|Built-in implementation
:-----|:-------|:----- -------|:-----
com.alipay.sofa.rpc.client.Client |Client | |Failover, Failfast
com.alipay.sofa.rpc.client.ConnectionHolder|Connection Holder | |AllConnect (all connections)
com.alipay.sofa.rpc.client.AddressHolder |Address Holder | | Single group, multiple groups
com.alipay.sofa.rpc.client.LoadBalancer |Load balancer| |Random, polling, least concurrency, consistency hash, native priority
com.alipay.sofa.rpc.client.Router |Router ||
com.alipay.sofa.rpc.codec.Compressor |Compressor | |snappy
com.alipay.sofa.rpc.codec.Serializer |Serializer | |Java, Hessian, PB
com.alipay.sofa.rpc.filter.Filter | Interceptor | |
com.alipay.sofa.rpc.protocol.Protocol |Protocol decoder | |Bolt, Dubbo, Rest
com.alipay.sofa.rpc.protocol.ProtocolDecoder | Protocol encoder| |Bolt
com.alipay.sofa.rpc.protocol.ProtocolEncoder |Protocol Encoding | |bolt
com.alipay.sofa.rpc.protocol.TelnetHandler | Telnet response| |Version, help, ls
com.alipay.sofa.rpc.proxy.Proxy |Proxy class | |Java, Javassist
com.alipay.sofa.rpc.registry.Registry | Registry Center | |ZooKeeper
com.alipay.sofa.rpc.server.Server |Server implementation | | Bolt, Rest
com.alipay.sofa.rpc.transport.ClientTransport | Client persistent connection implementation | | Netty
com.alipay.sofa.rpc.transport.ServerTransport | Server persistent connection implementation | | Netty
