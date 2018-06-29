# 客户端内置扩展 Metrics 指标

目前默认生效的的扩展模块是: lookout-ext-jvm 。 lookout-ext-os 暂时并未生效，可以按照 SPI 机制生效之。 

## JVM 线程
| metric name |  metric tags |  specification |
| --- |  --- |  --- |
| jvm.threads.totalStarted |   |  --- |
| jvm.threads.active |   |  --- |
| jvm.threads.peak |  |  --- |
| jvm.threads.daemon |   |  --- |

## JVM 类加载
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.classes.unloaded |   |  --- |
| jvm.classes.loaded |  |  --- |
| jvm.classes.total |  |  --- |

## JVM 内存
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.memory.heap.init |  |  --- |
| jvm.memory.heap.used |   |  --- |
| jvm.memory.heap.max |  |  --- |
| jvm.memory.heap.committed | |  --- |

## JVM 垃圾回收
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.gc.young.time |  |  --- |
| jvm.gc.young.count |  |  --- |
| jvm.gc.old.time |   |  --- |
| jvm.gc.old.count |   |  --- |

## 机器文件系统信息
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| instance.file.system.free.space | root（文件系统根目录名） |  --- |
| instance.file.system.total.space | root  |  --- |
| instance.file.system.usabe.space | root  |  --- |

## 机器信息
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| instance.mem.free |   |  --- |
| instance.mem.total |   |  --- |
| instance.processors |  |  --- |
| instance.uptime |   |  --- |
| instance.systemload.average |   |  --- |
