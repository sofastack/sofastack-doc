## 预热转发

| 属性 | 描述 | 默认值 | 备注 |
| :--- | :--- | :--- | :--- |
| during | 预热时间 | 0 |  |
| weightStarting | 预热期转发比例 | 1 | RPC框架内部会在集群中随机 找一台机器以此权重转出 |
| weightStarted | 预热完成后转发比例 | 0 | 预热结束后将会一直生效 |
| address | 预热期过后的转发地址 | 空 | 预热后将会一直生效 |
| uniqueId | 同 appName 多集群部署的情况下，要区别不同集群可以通过配置此项区分。指定一个自定义的系统变量（例如机房、分组等），保证集群唯一即可。 |  | 注意该uniqueId并不是配置字符串中的uniqueid。在纯RPC环境中，需要手动设置到TransmitConfig中；在SOFABoot 环境中，配置字符串中的uniqueId作为key，SOFA Boot 会通过该key动态从配置文件中读取value作为uniqueid值设置到RPC中。 |
