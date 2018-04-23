## Bug Fix
- 修复无法读取 `long[]` 的bug。
- 修复 `ArrayDeserializer` 的找不到类不抛出异常的bug。

## Function
- 增加类名解析器（可用做类名转换，类名黑白名单等）。
- `SerializerFactory` 增加默认开启序列化黑名单功能。
- 增加泛化序列化能力。

## Optimization
- 改进 `SerializerFactory` 里的缓存的锁机制。
- 如果默认构造函数传入`null`创建实例时抛出异常，那么使用`unsafe()`方法进行实例化。