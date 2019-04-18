# 客户端内置扩展 Metrics 指标

目前默认生效的的扩展模块是: lookout-ext-jvm ， lookout-ext-os(from v1.5.0)。

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

## Linux 操作系统信息 （1.5.0版本之后默认启用）
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| os.systemload.average.1min |   |  --- |
| os.systemload.average.5min |   |  --- |
| os.systemload.average.15min |   |  --- |
| os.cpu.idle |   |  --- |
| os.cpu.iowait |   |  --- |
| os.cpu.irq |   |  --- |
| os.cpu.nice |   |  --- |
| os.cpu.softirq |   |  --- |
| os.cpu.system |   |  --- |
| os.cpu.user |   |  --- |
| os.disk.usage.percent.used | device,root,type  |  --- |
| os.disk.usage.total.bytes | device,root,type  |  --- |
| os.disk.usage.used.bytes |  device,root,type |  --- |
| os.net.stats.in.bytes |  intfc |  --- |
| os.net.stats.in.compressed |  intfc |  --- |
| os.net.stats.in.dropped |  intfc |  --- |
| os.net.stats.in.errs |  intfc |  --- |
| os.net.stats.in.fifo.errs |  intfc |  --- |
| os.net.stats.in.frame.errs |  intfc |  --- |
| os.net.stats.in.multicast |  intfc |  --- |
| os.net.stats.in.packets |  intfc |  --- |
| os.net.stats.out.bytes |  intfc |  --- |
| os.net.stats.out.carrier.errs |  intfc |  --- |
| os.net.stats.out.collisions |  intfc |  --- |
| os.net.stats.out.compressed |  intfc |  --- |
| os.net.stats.out.dropped |  intfc |  --- |
| os.net.stats.out.errs |  intfc |  --- |
| os.net.stats.out.fifo.errs |  intfc |  --- |
| os.net.stats.out.packets |  intfc |  --- |
| os.memory.stats.buffers.bytes | --- | >= 1.5.3 |
| os.memory.stats.cached.bytes | --- |   >= 1.5.3 |
| os.memory.stats.free.bytes | --- |   >= 1.5.3 |
| os.memory.stats.total.bytes | --- |   >= 1.5.3 |
