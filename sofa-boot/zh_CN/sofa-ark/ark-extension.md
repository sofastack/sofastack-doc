Ark  容器和 Ark Plugin 在运行时由不同的类加载器加载，不能使用常规的 ServiceLoader 提供 SPI 扩展，SOFAArk 自定义扩展点 SPI 机制， Ark Plugin 实现 SPI 机制，考虑到 Biz 卸载问题，Ark Biz 暂时不支持该 SPI 机制，只适用于 Ark Plugin 之间。

### 声明扩展接口
使用注解 `@Extensible` 声明扩展接口，注解定义如下：
```java
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Extensible {

    /**
     * return specify extensible file name, default value is the
     * full name of interface.
     */
    String file() default "";

    /**
     * return whether this a singleton, with a single, shared instance
     * returned on all calls, default value is true.
     */
    boolean singleton() default true;
}
```
+ `file` 用于声明 SPI 扩展文件名，默认为接口全类名
+ `singleton` 用于声明加载扩展类是否为单例模式

### 声明扩展实现
使用注解 `@Extension` 声明扩展实现，注解定义如下：
```java
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Extension {
    /**
     * extension name
     */
    String value();

    /**
     * extension order, Higher values are interpreted as lower priority.
     * As a consequence, the object with the lowest value has the highest
     * priority.
     */
    int order() default 100;
}
```
+ `value` 用于定义扩展实现名称，例如不同的插件扩展同一个接口，可能会取不同的名字。
+ `order` 用于决定具体扩展实现的生效顺序

运行时，对于同一个接口的扩展实现生效规则如下：
+ 规则一：名称相同的扩展实现，只会返回优先级高的扩展实现类，order 数字越小，优先级越高
+ 规则二：名称不相同的扩展实现，则返回一个对应的 List 列表，每个名称返回优先级最高的扩展实现

### 加载 SPI 实现类
正常情况下，我们使用 ServiceLoader 加载 SPI 接口实现；SOFAArk 提供了工具类 `ArkServiceLoader` 用于加载扩展实现，工具类定义了两个简单的方法：
```java
public class ArkServiceLoader {
    private static ExtensionLoaderService extensionLoaderService;

    // 方法一
    public static <T> T loadExtension(Class<T> interfaceType, String extensionName) {
        return extensionLoaderService.getExtensionContributor(interfaceType, extensionName);
    }

    // 方法二
    public static <T> List<T> loadExtension(Class<T> interfaceType) {
        return extensionLoaderService.getExtensionContributor(interfaceType);
    }
}
```

+ 方法一：用于加载指定接口和名称的扩展实现，返回单个结果。参考上述规则一
+ 方法二：用于加载指定接口的扩展实现，返回列表结果。参考上述规则二

需要注意下，定义 SPI 接口的插件需要导出该接口，负责实现 SPI 接口的插件需要导入该接口。另外 SOFAArk 容器本身也会定义部分用于插件扩展实现的 SPI 接口，例如 `ClassLoaderHook`

### 为什么不支持 Biz 的 SPI 扩展实现加载
考虑到 Biz 会动态的安装和卸载，如果支持 Biz 的扩展实现加载，生命周期容易引起混乱，暂时不考虑支持。如果确实存在 Ark Plugin  需要主动触发 Ark  Biz 的逻辑调用，可以通过 SOFAArk 内部事件机制。

### SOFAArk 默认扩展点
SOFAArk 容器目前提供了唯一一个扩展点 `ClassLoaderHook`，用于其他插件提供扩展实现，自定义类/资源加载逻辑。`ClassLoaderHooker` 接口定义如下，用于扩展 BizClassLoader 和 PluginClassLoader 类(资源）加载逻辑：

```java
@Extensible
public interface ClassLoaderHook<T> {

    Class<?> preFindClass(String name, ClassLoaderService classLoaderService, T t)
                                                                                  throws ClassNotFoundException;


    Class<?> postFindClass(String name, ClassLoaderService classLoaderService, T t)
                                                                                   throws ClassNotFoundException;


    URL preFindResource(String name, ClassLoaderService classLoaderService, T t);


    URL postFindResource(String name, ClassLoaderService classLoaderService, T t);


    Enumeration<URL> preFindResources(String name, ClassLoaderService classLoaderService, T t)
                                                                                              throws IOException;

    Enumeration<URL> postFindResources(String name, ClassLoaderService classLoaderService, T t)
                                                                                               throws IOException;
}
```

通过在插件中扩展该 SPI 接口实现，可以自定义 PluginClassLoader 和  BizClassLoader 的类/资源的加载逻辑。

#### 扩展实现 PluginClassLoader 加载逻辑
定义对 PluginClassLoader 的扩展实现，需要指定 extension 名为 `plugin-classloader-hook`; 这是因为目前 SOFAArk 的策略只允许一个Plugin ClassLoaderHook 扩展实现生效，如果同时定义多个扩展类，优先级最高的生效。
```java
@Extension("plugin-classloader-hook")
public class TestPluginClassLoaderHook implements ClassLoaderHook<Plugin> {
}
```

#### 扩展实现 BizClassLoader 加载逻辑
定义对 BizClassLoader 的扩展实现，需要指定 extension 名为 `biz-classloader-hook`; 理由同上，目前 SOFAArk 的策略只允许一个Biz ClassLoaderHook 扩展实现生效，如果同时定义多个扩展类，优先级最高的生效。
```java
@Extension("biz-classloader-hook")
public class TestBizClassLoaderHook implements ClassLoaderHook<Biz> {
}
```
