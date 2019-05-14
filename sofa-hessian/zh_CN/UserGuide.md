# 快速开始

## 引入 SOFA-Hessian

在工程的 `pom.xml` 加入如下依赖即可。

```xml
<dependency>
    <groupId>com.alipay.sofa</groupId>
    <artifactId>hessian</artifactId>
    <version>${hessian.version}</version>
</dependency>
```

## 序列化与反序列化 

SOFA-Hessian 的序列化和反序列化操作方式和原生 Hessian 是相同的。

下面演示的是一个序列化和反序列化的过程，示例参见：[SerializerExample.java](https://github.com/sofastack/alipay-hessian/blob/master/src/test/java/com/alipay/hessian/example/SerializerExample.java)


```java
public static void main(String[] args) throws IOException {
    // Initial SerializerFactory
    // It is highly recommended to cache this factory for every serialization and deserialization.
    SerializerFactory serializerFactory = new SerializerFactory();

    // Do serializer
    String src = "xxx";
    ByteArrayOutputStream bout = new ByteArrayOutputStream();
    Hessian2Output hout = new Hessian2Output(bout);
    hout.setSerializerFactory(serializerFactory);
    hout.writeObject(src);
    hout.close();
    byte[] data = bout.toByteArray();

    // Do deserializer
    ByteArrayInputStream bin = new ByteArrayInputStream(data, 0, data.length);
    Hessian2Input hin = new Hessian2Input(bin);
    hin.setSerializerFactory(new SerializerFactory());
    String dst = (String) hin.readObject();
    hin.close();
    System.out.println(dst);
}
```


# 进阶指南

## ClassNameResolver & ClassNameFilter
SOFA-Hessian 内置了一个类名决策机制，通过 `ClassNameResolver` 实现， `ClassNameResolver` 内可以设置一组 `ClassNameFilter`。

在序列化和反序列化的时候，会调用 `ClassNameResolver.resolve()` 方法，调用 `ClassNameFilter` 会对类名进行过滤，过滤过程可以对类名进行转换或者抛出异常。

SOFA-Hessian 从 v3.3.0 开始会自动加入一个内置黑名单过滤器，该黑名单来自蚂蚁的安全团队。黑名单内的类将无法序列化和反序列化。

当然你也可以自定义类名过滤器。

示例参见：[ClassNameResolverExample.java](https://github.com/sofastack/alipay-hessian/blob/master/src/test/java/com/alipay/hessian/example/ClassNameResolverExample.java)

```java
public static void main(String[] args) throws IOException {
    // Initial SerializerFactory
    // It is highly recommended to cache this factory for every serialization and deserialization.
    SerializerFactory serializerFactory = new SerializerFactory();

    // Do serializer
    try {
        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        Hessian2Output hout = new Hessian2Output(bout);
        hout.setSerializerFactory(serializerFactory);
        hout.writeObject(new Socket()); // will throw an exception because java.net.Socket is in the serialize blacklist
    } catch (Exception e) {
        e.printStackTrace();
    }

    try {
        // define custom ClassNameResolver
        ClassNameResolver resolver = new ClassNameResolver();
        resolver.addFilter(new ClassNameFilter() {
            @Override
            public int order() {
                return 0;
            }

            @Override
            public String resolve(String className) throws IOException {
                if (className.equals(Thread.class.getName())) {
                    throw new IOException("Thread is not allowed.");
                }
                return className;
            }
        });
        // replace default ClassNameResolver
        serializerFactory.setClassNameResolver(resolver);

        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        Hessian2Output hout = new Hessian2Output(bout);
        hout.setSerializerFactory(serializerFactory);
        hout.writeObject(new Thread()); // will throw an exception because java.net.Thread is not allowed
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

## 泛化序列化
一般情况下，服务端会将Java类的Class文件给到客户端，双方使用统一的Class类作为序列化/反序列化的标准。

而泛化序列化是指客户端不再依赖服务端的Class类定义，而是客户端直接通过泛化类，组装出服务端需要的数据结构。服务端接收到的是正常的Hessian序列化后的数据，不感知客户端是否使用泛化序列化。

示例参见: [TestObj.java](https://github.com/sofastack/alipay-hessian/blob/master/src/test/java/com/alipay/hessian/example/TestObj.java)、[GenericExample.java](https://github.com/sofastack/alipay-hessian/blob/master/src/test/java/com/alipay/hessian/example/GenericExample.java)

假如我们定义了一个自定义类:

```java
public class TestObj {
    private String name;
    private int age;
    private List<String> nickNames;
    // getter and setter
}
```
此时客户端如果没有这个类，我们需要组装一个 `GenericObject` 进行序列化和反序列化。

```java
public static void main(String[] args) throws IOException {
    // Initial SerializerFactory
    // It is highly recommended to cache this factory for every serialization and deserialization.
    SerializerFactory serializerFactory = new SerializerFactory();
    GenericSerializerFactory genericSerializerFactory = new GenericSerializerFactory();

    // define generic object
    GenericObject genericObject = new GenericObject("com.alipay.hessian.example.TestObj");
    genericObject.putField("name", "xxx");
    genericObject.putField("age", 25);
    GenericArray nickList = new GenericArray("java.lang.String");
    nickList.setObjects(new String[]{"xa", "xb", "xc"});
    genericObject.putField("nickNames", nickList);

    // Do serializer
    ByteArrayOutputStream bout = new ByteArrayOutputStream();
    Hessian2Output hout = new Hessian2Output(bout);
    hout.setSerializerFactory(genericSerializerFactory); // set to genericSerializerFactory
    hout.writeObject(genericObject);
    hout.close();
    byte[] data = bout.toByteArray();

    // deserializer to native TestObj
    ByteArrayInputStream bin = new ByteArrayInputStream(data, 0, data.length);
    Hessian2Input hin = new Hessian2Input(bin);
    hin.setSerializerFactory(serializerFactory);
    TestObj dst = (TestObj) hin.readObject();
    hin.close();
    System.out.println(dst.getName());
    System.out.println(dst.getAge());
    System.out.println(dst.getNickNames());
}
```


# FAQ
1. SOFARPC 里引用的 `SOFA-Hessian` 与业务使用的 `Hessian` 类冲突了怎么办？
> 可以使用 SOFA Boot 类隔离能力，参见 [SOFA Ark](https://github.com/sofastack/sofa-ark)