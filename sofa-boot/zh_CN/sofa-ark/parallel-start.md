SOFABoot 会根据 Require-Module 计算模块依赖树，例如以下依赖树表示模块B 和模块C 依赖模块A，模块E 依赖模块D，模块F 依赖模块E：

![undefined](./module-parallel.png) 

该依赖树会保证模块A 必定在模块B 和模块C 之前启动，模块D 在模块E 之前启动，模块E 在模块F 之前启动，但是依赖树没有定义模块B 与模块C，模块B、C与模块D、E、F之间的启动顺序，这几个模块之间可以串行启动，也可以并行启动。

SOFABoot 默认会并行启动模块，在使用过程中，如果希望关闭并行启动，可以在 application.properties 中增加以下参数:

```java
com.alipay.sofa.boot.module-start-up-parallel=false
```
