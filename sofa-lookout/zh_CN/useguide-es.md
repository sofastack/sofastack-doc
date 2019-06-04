# SOFALookout 的 ES 存储使用

## 自动化
推荐版本: ES 5
### 自动初始化库

Lookout 服务器端启动时，会自动检查（默认开启，可关闭）所连接的ES机器(或集群)，检查 Metrics 数据存储的 Index和 Mapping 是否已经建立，
如果未初始化则进行初始化工作。默认初始化并产生索引alias: "lookout-active-metrics，lookout-search-metrics"。

- 看下 Alias 和 Indices
```
http://localhost:9200/_cat/aliases

lookout-active-metrics metrics-2019.05.30-1 - - -
lookout-search-metrics metrics-2019.05.30-1 - - -
```

- 看下存储 Mapping：
```
http://localhost:9200/lookout-active-metrics/_mapping

{
  "metrics-2019.05.30-1": {
    "mappings": {
      "metrics": {
        "properties": {
          "id": {
            "type": "keyword"
          },
          "tags": {
            "type": "keyword"
          },
          "time": {
            "type": "date"
          },
          "value": {
            "type": "float"
          }
        }
      }
    }
  }
}
```
### 自动运维

- 自动 Indices Rollover
  * 如果超过1天，则切换新索引
  * 如果单个索引的 docs 数目超过: 100000000,则切换新索引；
  
- 自动删除过期 Indices

 默认最多只保留 7 天的数据，过期的 Index 会自动检查并被删除；

