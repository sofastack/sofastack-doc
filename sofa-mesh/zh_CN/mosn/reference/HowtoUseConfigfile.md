# 配置文件说明

+ 配置文件示例

```json
{
    "servers": [
        {
            "access_log": "logs/access.log", 
            "log_level": "INFO", 
            "network_filters": [
                {
                    "type": "proxy", 
                    "config": {
                        "downstream_protocol": "sofarpc", 
                        "upstream_protocol": "http2", 
                        "routes": [
                            {
                                "name": "example_route", 
                                "service": ".*", 
                                "cluster": "local_service"
                            }
                        ]
                    }
                }
            ], 
            "stream_filters": [
                {
                    "type": "fault_inject", 
                    "config": {
                        "delay_percent": 100, 
                        "delay_duration_ms": 2000
                    }
                }, 
                {
                    "type": "healthcheck", 
                    "config": {
                        "passthrough": false, 
                        "cache_time_ms": 360000, 
                        "cluster_min_healthy_percentages": {
                            "local_service": 70
                        }
                    }
                }
            ], 
            "listeners": [
                {
                    "name": "default_sofa", 
                    "address": "127.0.0.1:12200", 
                    "bind_port": true
                }, 
                {
                    "name": "extra_sofa", 
                    "address": "127.0.0.1:12201", 
                    "bind_port": true
                }
            ]
        }
    ], 
    "tracing": {
        "http": {
            "driver": {
                "type": "zipkin", 
                "config": {
                    "collector_cluster": "jaeger", 
                    "collector_endpoint": "/api/v1/spans"
                }
            }
        }
    }, 
    "cluster_manager": {
        "clusters": [
            {
                "name": "local_service", 
                "type": "static", 
                "lb_type": "LB_ROUNDROBIN", 
                "max_request_per_conn": 100000, 
                "conn_buff_limit_bytes": 100000, 
                "healthcheck": {
                    "timeout": 1000, 
                    "healthy_threshold": 3, 
                    "unhealthy_threshold": 5, 
                    "interval": 6000, 
                    "interval_jitter": 1000
                }, 
                "hosts": [
                    {
                        "hostname": "local_upstream", 
                        "address": "127.0.0.1:8088", 
                        "weight": 1
                    }
                ]
            }, 
            {
                "name": "local_service_2", 
                "type": "static", 
                "lb_type": "LB_ROUNDROBIN", 
                "hosts": [
                    {
                        "hostname": "local_upstream", 
                        "address": "127.0.0.1:8088", 
                        "weight": 10
                    }, 
                    {
                        "hostname": "local_upstream", 
                        "address": "127.0.0.1:8089", 
                        "weight": 90
                    }
                ]
            }
        ]
    }
}
```
+ 配置结构体
与上述配置文件中的结构对应，和api.v2包下的结构保持大致对应。

+ `ServerConfig`
对应一个WebServer, 包含属性见结构体定义

```go
type ServerConfig struct {
	AccessLog      string         `json:"access_log"`
	LogLevel       string         `json:"log_level"`
	NetworkFilters []FilterConfig `json:"network_filters"`
	StreamFilters  []FilterConfig `json:"stream_filters"`
	Listeners      []ListenerConfig
}
```

+ `FilterConfig`
对应各种Filte配置, 由于不同的filter所携带的属性各不相同, 因此约定如下:
Filter需要声明自己的类型Type
Filter自身所需要的属性通过Config字段来配置，结构为json object
```go
type FilterConfig struct {
	Type   string
	Config map[string]interface{}
}
```

示例:
```json
{
	"type": "fault_inject", 
	"config": {
    	"delay_percent": 100, 
		"delay_duration_ms": 2000
	}
}
```

+ `ListenerConfig`
对应一个监听器对象

```go
type ListenerConfig struct {
	Name       string
	Address    string
	BindToPort bool `json:"bind_port"`
}
```

+ `ClusterConfig`
对应一个Cluster

```go
type ClusterConfig struct {
	Name                 string
	Type                 string
	LbType               string `json:"lb_type"`
	MaxRequestPerConn    uint64 `json:"max_request_per_conn"`
	ConnBufferLimitBytes int
	HealthCheck          HealthCheckConfig `json:"healthcheck"`
	Hosts                []HostConfig
}
```

+ `HostConfig`
对应一个Host

```go
type HostConfig struct {
	Address  string
	Hostname string
	Weight   uint32
}
```