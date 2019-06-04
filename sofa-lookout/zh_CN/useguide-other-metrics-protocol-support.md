# 服务器端-常见数据采集协议支持

使用 Lookout sdk是推荐方式，当然 Lookout gateway 还支持其他协议上报。 （但由于属于非标接入，细节可联系我们）

注意如果使用 **非lookout sdk ，自己一定注意控制客户端metrics数量！ [**[Don't over use labels(tags)](https://prometheus.io/docs/practices/instrumentation/#do-not-overuse-labels)**]**

## 1.Promethues Push协议写入支持

- Lookout-gateway这里扮演的是一个 prometheus-pushgateway 角色：


```
echo "some_metric{k1="v1"} 3.14" | curl --data-binary \
 @- http://localhost:7200/prom/metrics/job/{job}/app/{app}/step/{step}
```

- 区别在于："[http://localhost:7200/prom/](http://localhost:7200/prom/)"，端口为`7200`，加了级主路径为`/prom`.

- 【必选】URL路径变量 {app}  {job} 和{step}，必须要指定哦。step 单位秒，表示您定时上报的时间间隔（假如10s 上报一次数据，那么 step=10）

- 【可选】如果和lookout gateway间有网络代理，建议URL 里也附带上客户端真实 ip （如 "/ip/{ip}"）。

- 上报格式样式: 【 http_requests_total{method="post",code="200"} 1027 】，多个以换行符【'\n'】分割；

- 更多细节可以参考：[prometheus-pushgateway](https://github.com/prometheus/pushgateway)  ，你可以选择官方[对应编程语言的SDKs](https://prometheus.io/docs/instrumenting/clientlibs/)


## 2. Lookout 自有协议写入支持

默认的收集服务和数据协议标准(即Lookout自有的协议支持标准)

- localhost:7200/lookout/metrics/app/{app}/step/{step}


```
curl -H "Content-type:text/plain"  -X POST -d 'xx' \
 localhost:7200/lookout/metrics/app/{app}/step/{step}
```

- 请求体是一种批量复合形式。内容是多条 metrics 数据以 "\t" 进行连接；


```
{"time":"1970-01-01T08:00:00+08:00","tags":{"k1":"v1"},"m_name":{"count":0,"rate":0.0}}
\t{"time":"1970-01-01T08:00:00+08:00","tags":{"k1":"v1"},"m_name":{"value":99.0}}
\t{"time":"1970-01-01T08:00:00+08:00","tags":{"k1":"v1"},"m_name":{"elapPerExec":0.0,"totalTime":0.0,"max":0.0}}
\t{"time":"1970-01-01T08:00:00+08:00","tags":{"k1":"v1"},"m_name":{"totalAmount":0.0,"rate":0.0,"max":0}}
```
上面内容中组成部分分别是：counter型,gauge型,Timer型

- 其中单条数据结构

```
{
  "time": "1970-01-01T08:00:00+08:00",
  "tags": {
    "k1": "v1"
  },
  "m_name": {
    "count": 0,
    "rate": 0
  }
}
```

- tag 的 value 需要转义;

- 如果内容由进行了 snappy 压缩，需添加请求头 "Content-Encoding:snappy",且"Content-type: application/octet-stream";



## 3.OPEN TSDB 协议写入支持

- 请求demo


```
curl -X POST \
 http://localhost:7200/opentsdb/api/put \
 -H 'Content-Type: application/json' \
 -H 'step: 10000' \
 -H 'app: xx' \
 -H 'X-Lookout-Token: xx' \
 -d '[{
   "metric": "xzc.cpu",
   "timestamp": 1530624430,
   "value": 30,
   "tags": {
      "host": "web02",
      "dc": "lga"
   }
}]'
```

- 注意timestamp的单位是秒(而且尽量是当前时间附近哦，否则不太好查询)

- post的内容可以是一个json对象或json数组(批量模式)

- 更多细节可以参考 OpenTSDB的 /api/put 接口 [http://opentsdb.net/docs/build/html/api_http/put.html](http://opentsdb.net/docs/build/html/api_http/put.html)


## 4.Metricbeat 写入协议支持

### （1）.metricbeat的配置

配置文件 metricbeat.yml

```
output.elasticsearch:
  hosts: ['10.15.232.67:7200']
  path: /beat
```
host 是 lookout-gateway 的地址,端口是`7200`. 另外加了级主路径`/beat`;
### (2).为了符合metrics2.0标准，gateway会对数据进行转换

这块后续去时序库查询，你需要关注：

- FROM:

```
{
  "@timestamp": "2018-03-29T08:27:21.200Z",
  "metricset": {
    "name": "network",
    "module": "system",
    "rtt": 3487
  },
  "system": {
    "network": {
      "in": {
        "errors": 0,
        "dropped": 0,
        "bytes": 0,
        "packets": 0
      },
      "out": {
        "errors": 0,
        "dropped": 0,
        "packets": 0,
        "bytes": 0
      },
      "name": "ip_vti0"
    }
  },
  "beat": {
    "name": "moby",
    "hostname": "moby",
    "version": "6.2.3"
  }
}
```

- TO:


```
{
  "metrics": {
    "system.network.in.out.packets": 0,
    "system.network.in.out.errors": 0,
    "system.network.in.out.dropped": 0,
    "system.network.in.out.bytes": 0,
    "system.network.in.packets": 0,
    "system.network.in.errors": 0,
    "system.network.in.bytes": 0,
    "system.network.in.dropped": 0
  },
  "tags": {
    "beat.hostname": "moby",
    "beat.name": "moby",
    "metricset.name": "network",
    "system.network.in.name": "ip_vti0",
    "metricset.module": "system",
    "metricset.rtt": 3487,
    "beat.version": "6.2.3"
  },
  "timestamp": 1522312041200
}
```


