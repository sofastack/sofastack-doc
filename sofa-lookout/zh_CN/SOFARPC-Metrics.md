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
rpc.consumer.service.stats.fail_count.count| app,service,method,protocol,invoke_type,target_app |  某个具体接口失败次数 |
rpc.consumer.service.stats.fail_count.rate| app,service,method,protocol,invoke_type,target_app |  某个具体接口每秒失败 |
rpc.consumer.service.stats.fail_time.elapPerExec| app,service,method,protocol,invoke_type,target_app |  某个具体接口每秒执行时间 |
rpc.consumer.service.stats.fail_time.max| app,service,method,protocol,invoke_type,target_app |  某个具体接口失败时间最大值 |
rpc.consumer.service.stats.fail_time.totalTime| app,service,method,protocol,invoke_type,target_app |  某个具体接口失败时间总值 |
rpc.consumer.service.stats.request_size.max| app,service,method,protocol,invoke_type,target_app |  某个具体接口请求大小最大值 |
rpc.consumer.service.stats.request_size.rate| app,service,method,protocol,invoke_type,target_app |  某个具体接口每秒平均请求大小 |
rpc.consumer.service.stats.request_size.totalAmount| app,service,method,protocol,invoke_type,target_app |  某个具体接口请求大小总金额 |
rpc.consumer.service.stats.response_size.max| app,service,method,protocol,invoke_type,target_app |  某个具体接口响应大小最大值 |
rpc.consumer.service.stats.response_size.rate| app,service,method,protocol,invoke_type,target_app |  某个具体接口每秒平均响应大小 |
rpc.consumer.service.stats.response_size.totalAmount| app,service,method,protocol,invoke_type,target_app |  某个具体接口响应大小总金额 |
rpc.consumer.service.stats.total_count.count| app,service,method,protocol,invoke_type,target_app |  某个具体接口总的调用数目 |
rpc.consumer.service.stats.total_count.count_service_sum_30000| app,service,method,protocol,invoke_type,target_app |  某个具体接口总的调用信息 |
rpc.consumer.service.stats.total_count.rate| app,service,method,protocol,invoke_type,target_app |  某个具体接口每秒调用次数 |
rpc.consumer.service.stats.total_time.elapPerExec| app,service,method,protocol,invoke_type,target_app |  某个具体接口平均每次指定时间 |
rpc.consumer.service.stats.total_time.max| app,service,method,protocol,invoke_type,target_app |  某个具体接口总时间最大值 |
rpc.consumer.service.stats.total_time.totalTime| app,service,method,protocol,invoke_type,target_app |  某个具体接口总时间 |


## 服务端被调用信息
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
rpc.provider.service.stats.fail_count.count|app,service,method,protocol,caller_app  |  某个具体接口总的被调用失败次数 |
rpc.provider.service.stats.fail_count.rate|app,service,method,protocol,caller_app  |  某个具体接口每秒失败次数 |
rpc.provider.service.stats.fail_time.elapPerExec|app,service,method,protocol,caller_app  |  某个具体接口每次失败失败 |
rpc.provider.service.stats.fail_time.max|app,service,method,protocol,caller_app  |  某个具体接口失败次数最大值 |
rpc.provider.service.stats.fail_time.totalTime|app,service,method,protocol,caller_app  |  某个具体接口失败总时间 |
rpc.provider.service.stats.total_count.count|app,service,method,protocol,caller_app  |  某个具体接口总的调用次数 |
rpc.provider.service.stats.total_count.rate|app,service,method,protocol,caller_app  |  某个具体接口每秒调用次数 |
rpc.provider.service.stats.total_time.elapPerExec|app,service,method,protocol,caller_app  |  某个具体接口每次调用时间 |
rpc.provider.service.stats.total_time.max|app,service,method,protocol,caller_app  |  某个具体接口总时间最大值 |
rpc.provider.service.stats.total_time.totalTime|app,service,method,protocol,caller_app  |  某个具体接口总时间 |
