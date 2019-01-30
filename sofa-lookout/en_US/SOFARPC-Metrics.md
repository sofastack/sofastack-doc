# SOFARPC Metrics

SOFARPC currently measures two metrics.

## Server thread pool
| metric name |  metric tags |  specification |
| --- | --- | --- |
| rpc.bolt.threadpool.config | bolt thread pool configuration | Mainly includes thread pool configuration information for RPC server |
| rpc.bolt.threadpool.active.count | | Running thread of the current thread pool |
| rpc.bolt.threadpool.idle.count | | Idle thread of the current thread pool |
| rpc.bolt.threadpool.queue.size | | Tasks in the queue of the current thread pool |


## Client call information
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
rpc.consumer.service.stats.fail_count.count| app,service,method,protocol,invoke_type,target_app |  Failure count of a certain interface |
rpc.consumer.service.stats.fail_count.rate| app,service,method,protocol,invoke_type,target_app |  Number of failures per second of a certain interface |
rpc.consumer.service.stats.fail_time.elapPerExec| app,service,method,protocol,invoke_type,target_app | Average time per failed execution of a certain interface |
rpc.consumer.service.stats.fail_time.max| app,service,method,protocol,invoke_type,target_app |  Maximum failure time of a certain interface |
rpc.consumer.service.stats.fail_time.totalTime| app,service,method,protocol,invoke_type,target_app |  Total failure time of a certain interface |
rpc.consumer.service.stats.request_size.max| app,service,method,protocol,invoke_type,target_app |  Maximum request size of a certain interface |
rpc.consumer.service.stats.request_size.rate| app,service,method,protocol,invoke_type,target_app |  Average request size per second of a certain interface |
rpc.consumer.service.stats.request_size.totalAmount| app,service,method,protocol,invoke_type,target_app | Total request amount of a certain interface |
rpc.consumer.service.stats.response_size.max| app,service,method,protocol,invoke_type,target_app |  Maximum response size of a certain interface |
rpc.consumer.service.stats.response_size.rate| app,service,method,protocol,invoke_type,target_app |  Average response size per second of a certain interface |
rpc.consumer.service.stats.response_size.totalAmount| app,service,method,protocol,invoke_type,target_app | Total response amount of a certain interface |
rpc.consumer.service.stats.total_count.count| app,service,method,protocol,invoke_type,target_app |  Total number of calls of a certain interface |
rpc.consumer.service.stats.total_count.count_service_sum_30000| app,service,method,protocol,invoke_type,target_app | Total call information of a certain interface |
rpc.consumer.service.stats.total_count.rate| app,service,method,protocol,invoke_type,target_app |  Number of calls per second of a certain interface |
rpc.consumer.service.stats.total_time.elapPerExec| app,service,method,protocol,invoke_type,target_app | Average time per execution of a certain interface |
rpc.consumer.service.stats.total_time.max| app,service,method,protocol,invoke_type,target_app | Maximum total time of a certain interface |
rpc.consumer.service.stats.total_time.totalTime| app,service,method,protocol,invoke_type,target_app | Total time of a certain interface |


## Server called information
| metric name |   metric tags |  specification |
| --- |  --- |  --- |
rpc.provider.service.stats.fail_count.count|app,service,method,protocol,caller_app  | Total number of failed calls of a certain interface |
rpc.provider.service.stats.fail_count.rate|app,service,method,protocol,caller_app  | Number of failures per second of a certain interface |
rpc.provider.service.stats.fail_time.elapPerExec|app,service,method,protocol,caller_app  | Time of each failure of a certain interface |
rpc.provider.service.stats.fail_time.max|app,service,method,protocol,caller_app  | Maximum number of failures of a certain interface |
rpc.provider.service.stats.fail_time.totalTime|app,service,method,protocol,caller_app  | Total failure time of a certain interface |
rpc.provider.service.stats.total_count.count|app,service,method,protocol,caller_app  | Total number of calls of a certaininterface |
rpc.provider.service.stats.total_count.rate|app,service,method,protocol,caller_app  | Number of calls per second of a certain interface |
rpc.provider.service.stats.total_time.elapPerExec|app,service,method,protocol,caller_app  | Time of each call of a certain interface |
rpc.provider.service.stats.total_time.max|app,service,method,protocol,caller_app  | Maximum total time of a certain interface |
rpc.provider.service.stats.total_time.totalTime|app,service,method,protocol,caller_app  | Total time of a certain interface |