# Instructions on performance report 
The following performance report presents the performance comparison data of SOFAMosn 0.1.0 and envoy in terms of pure TCP forwarding for Bolt and HTTP1.x protocols, mainly including QPS, RTT, failure rate, success rate and other indicators.

It is significant to note the following optimizations in v0.1.0 which are intended to improve the forwarding performance of SOFAMosn. 

+ For thread model, SOFAMosn uses the worker goroutine pool to handle stream events, and uses two independent goroutines to handle read and write IO separately.
+ For single-core forwarding, in the case of specifying `P=1`, SOFAMosn binds CPU with cores to improve the call execution efficiency of the system and the locality affinity of cache.
+ For memory, in the case of binding single core, SOFAMosn uses SLAB-style recycling mechanism to improve reuse and reduce memory copy.
+ For IO, SOFAMosn mainly implements optimization by controlling the read/write buffer size, read/write timing, read/write frequency and other parameters.

The performance test data is as follows:

# TCP proxy performance data
For the same deployment mode, this report compares  SOFAMosn 0.1.0 and envoy for the upper-layer protocol Bolt (SOFARPC related protocol) and HTTP1.1 respectively.

## Deployment mode
The pressure test is deployed in a pure proxy mode. The client process accesses the server process through the SOFAMosn process and serves as a forwarding proxy. The client process, SOFAMosn process, and server process run on the machines which belong to different network segments. The network delay of the direct access from the client to server is about 2.5ms.

## Client
### Bolt protocol (send 1K string)
The client that sends the Bolt protocol data uses the online pressure generators developed by Ant Financial and deploys the SOFARPC client.

On the pressure generator performance page, you can see the QPS, success/failure counts, RT and other parameters.

### HTTP1.1 protocol (send 1K string)
Use ApacheBench/2.3. The test instructions are:

```bash
Ab -n $RPC -c $CPC -p 1k.txt -T "text/plain" -k http://11.166.161.136:12200/tcp_bench > ab.log.$CPU_IDX &
```

## Mesh machine specifications
The mesh runs in a container where the CPU is an exclusive logical core. The specifications are as follows:

| Category | Information |
| -------- | -------- |
| OS | 3.10.0-327.ali2008.alios7.x86_64 |
| CPU | Intel(R) Xeon(R) CPU E5-2650 v2 @ 2.60GHz X 1 |

## Upstream machine specifications
| Category | Information |
| -------- | -------- |
| OS | 2.6.32-431.17.1.el6.FASTSOCKET |
| CPU | Intel(R) Xeon(R) CPU E5620 @ 2.40GHz X 16 |

## Bolt protocol test result
### Performance data

| Indicators | SOFAMosn | Envoy|
| -------- | -------- | -------- |
| QPS | 103500 |104000 |
| RT | 16.23ms |15.88ms |
| MEM | 31m |18m |
| CPU | 100% |100% |

### Conclusion
For single-core TCP forwarding, there is little difference between SOFAMosn 0.1.0 and Envoy 1.7 in terms of performance in the condition with full load, such as QPS, RTT and success/failure counts. We will continue to optimize in the subsequent versions.

## HTTP/1.1 test result
Since the HTTP/1.1 request response model is PING-PONG, QPS is positively correlated with the number of concurrences. The following tests are performed for different concurrence counts.
 
 ### Concurrences - 20
 | Indicators | SOFAMosn | Envoy|
| -------- | -------- | -------- |
| QPS | 5600 |5600 |
|RT (mean) | 3.549ms |3.545ms |
|RT (P99) | 4ms |4ms |
|RT (P98) | 4ms |4ms |
|RT (P95) | 4ms |4ms |
| MEM | 24m |23m |
| CPU | 40% |20% |
 
 ### Concurrences - 40
 | Indicators | SOFAMosn | Envoy
| -------- | -------- | -------- |
| QPS | 11150 |11200 |
RT (mean) | 3.583ms |3.565ms |
RT (P99) | 4ms |4ms |
RT (P98) | 4ms |4ms |
RT (P95) | 4ms |4ms |
| MEM | 34m |24m |
| CPU | 70% |40% |
 
 ### Concurrences - 200
 | Indicators | SOFAMosn | Envoy|
| -------- | -------- | -------- |
| QPS | 29670 |38800 |
|RT (mean) | 5.715ms |5.068ms |
|RT (P99) | 16ms |7ms |
|RT (P98) | 13ms |7ms |
|RT (P95) | 11ms |6ms |
| MEM | 96m |24m |
| CPU | 100% |95% |

 ### Concurrences - 220

| Indicators | SOFAMosn | Envoy
| -------- | -------- | -------- |
| QPS | 30367 |41070 |
|RT (mean) | 8.201ms |5.369ms |
|RT (P99) | 20ms |9ms |
|RT (P98) | 19ms |8ms |
|RT (P95) | 16ms |8ms |
| MEM | 100m |24m |
| CPU | 100% |100% |

### Conclusion
When the upper-layer protocol is HTTP/1.X., a certain gap exists between SOFAMosn and Envoy in terms of performance.

The preliminary conclusion is that in the PING-PONG package delivery model, MOSN cannot merge the read/write system calls. In the scenario where SOFARPC can merge the calls, SOFAMosn's HTTP performance is weaker than that of Envoy because the syscall count significantly increases. 

This problem will be optimized in version 0.2.0.

# Appendix

## Envoy version information
version:1.7
Tag:1ef23d481a4701ad4a414d1ef98036bd2ed322e7


## Envoy TCP test configuration

``` yaml
static_resources:
  listeners:
  - address:
      socket_address:
        address: 0.0.0.0
        port_value: 12200
    filter_chains:
    - filters:
      - name: envoy.tcp_proxy
        config:
          stat_prefix: ingress_tcp
          cluster: sofa_server
  clusters:
  - name: sofa_server
    connect_timeout: 0.25s
    type: static
    lb_policy: round_robin
    hosts:
    - socket_address:
        address: 10.210.168.5
        port_value: 12222
    - socket_address:
        address: 10.210.168.5
        port_value: 12223
    - socket_address:
        address: 10.210.168.5
        port_value: 12224
    - socket_address:
        address: 10.210.168.5
        port_value: 12225
admin:
  access_log_path: "/dev/null"
  address:
    socket_address:
      address: 0.0.0.0
      port_value: 8001
```
