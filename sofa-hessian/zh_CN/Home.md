SOFA-Hessian 基于原生 Hessian v3.1.3 进行改进，目前已经蚂蚁金服内部稳定运行多年。我们修复了一些 bug，增强了一些功能，并且添加了一些特性，包括：

- 增加泛化序列化。
- 增加 `ClassNameResolver` 和 `ClassNameFilter` 用于类名的映射、转换、过滤等。
- 增加序列化黑名单（来自蚂蚁金服安全团队）。
- 改进 `SerializerFactory` 内缓存的锁机制。
- 更多参见：[改进点](https://github.com/sofastack/sofa-hessian/wiki/Improvements)。

## 文档
 - [发布历史](https://github.com/sofastack/sofa-hessian/wiki/ReleaseNotes)
 - [用户手册](https://github.com/sofastack/sofa-hessian/wiki/UserGuide)
 - [改进点](https://github.com/sofastack/sofa-hessian/wiki/Improvements)
 - [发展路线](https://github.com/sofastack/sofa-hessian/wiki/RoadMap)
