# Client built-in extension metrics

The extension modules currently in effect by default are lookout-ext-jvm and lookout-ext-os (from v1.5.0).

## JVM thread
| metric name |  metric tags |  specification |
| --- |  --- |  --- |
| jvm.threads.totalStarted |   |  --- |
| jvm.threads.active |   |  --- |
| jvm.threads.peak |  |  --- |
| jvm.threads.daemon |   |  --- |

## JVM class loading
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.classes.unloaded |   |  --- |
| jvm.classes.loaded |  |  --- |
| jvm.classes.total |  |  --- |

## JVM memory
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.memory.heap.init |  |  --- |
| jvm.memory.heap.used |   |  --- |
| jvm.memory.heap.max |  |  --- |
| jvm.memory.heap.committed | |  --- |

## JVM garbage recycling
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| jvm.gc.young.time |  |  --- |
| jvm.gc.young.count |  |  --- |
| jvm.gc.old.time |   |  --- |
| jvm.gc.old.count |   |  --- |

## Machine file system information
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| instance.file.system.free.space | root（the available filesystem roots）|  --- |
| instance.file.system.total.space | root  |  --- |
| instance.file.system.usabe.space | root  |  --- |

## Machine information
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| instance.mem.free |   |  --- |
| instance.mem.total |   |  --- |
| instance.processors |  |  --- |
| instance.uptime |   |  --- |
| instance.systemload.average |   |  --- |

## Linux operating system information (enabled by default after version 1.5.0)
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
