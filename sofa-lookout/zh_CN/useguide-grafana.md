# 使用 Grafana

由于 SOFALookout Metrics Server 兼容 Prometheus API,所以 Grafana 集成 Lookout 很简单，只需要选择 Prometheus 作为数据源协议即可
（注意 Lookout Server 的默认查询端口也是: 9090）。

下图展示 Grafana 新增数据源配置:

![新增数据源](https://gw.alipayobjects.com/mdn/rms_e6b00c/afts/img/A*zYFsQZPJMP4AAAAAAAAAAABkARQnAQ)

使用 PromQL 查询展示数据:

![实例图表](https://gw.alipayobjects.com/mdn/rms_e6b00c/afts/img/A*j1Z9R65543MAAAAAAAAAAABkARQnAQ)
