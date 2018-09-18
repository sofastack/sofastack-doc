# SOFARPC Metrics 指标

 SOFARPC 目前度量了两个指标.

## 服务端线程池
| metric name |  metric tags |  specification |
| --- |  --- |  --- |
| rpc.bolt.threadpool.config | bolt 线程池配置  |  主要包括 rpc 服务端的线程池配置信息 |
| rpc.bolt.threadpool.active.count |   | 当前线程池的运行线程 |
| rpc.bolt.threadpool.idle.count |  |  当前线程池的空闲线程 |
| rpc.bolt.threadpool.queue.size |   |  当前线程池的队列中的任务 |


## 客户端调用信息
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| rpc.consumer.service.stats.*| app,service,method,protocol,invoke_type,target_app |  某个具体接口总的调用信息 |

## 客户端被调用信息
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
| rpc.provider.service.stats.*|app,service,method,protocol,caller_app  |  某个具体接口总的被调用信息 |

