# Instructions on performance report 

The baseline version of the following performance report is SOFAMosn 0.2.1. In version 0.2.1, we have made the following optimizations:

- Added memory reuse framework that covers the io/protocol/stream/proxy layers, thus reducing object allocation, memory usage, and GC stress.
- For the scenario where there are a large number of connections, added the Raw Epoll mode. This mode uses the event callback mechanism + IO goroutine pool to avoid stack memory consumption and scheduling overhead caused by massive goroutines.

Note that, since the pressure test tools for SOFARPC and h2 have no pxx indicators presented, the data selected in the performance report is **average**. In the future, we will improve the relevant pressure test environment tools to supplement relevant indicators (P99,P95...).

---

# Overview

This performance report added several scenarios based on v0.1.0 performance report. The report mainly contains the following 3 parts:
- Single-core performance (Sidecar)
   - Layer-7 proxy
     - Bolt (connected)
     - Http/1.1 (connected)
     - Http/2 (connected)
- Multi-core performance (gateway)
   - Layer-7 proxy
     - Bolt (direct connected)
     - Http/1.1 (direct connected)
     - Http/2 (direct connected)
- Persistent connection gateway
     - Bolt (read/write loop with goroutine/Raw Epoll)

# Single-core performance (SideCar)

## Test environment

### Machine information

| Machine | OS | CPU |
| -------- | -------- | -------- |
| 11.166.190.224 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2640 v3 @ 2.60GHz |
| 11.166.136.110 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz |
| bolt client |  The client serves as the load platform which consists of 5 load generators. It is estimated that there may be 500 connections between the client and client MOSN.|
| http1 client(10.210.168.5) | ApacheBench/2.3  | -n 2000000 -c 500 -k |
| http2 client(10.210.168.5) | nghttp.h2load  | -n1000000 -c5 -m100 -t4 |

### Deployment structure

| Test mode | Deployment structure |
| -------- | -------- |
| Connected     | client --> mosn(11.166.190.224) --> mosn(11.166.136.110) --> server(11.166.136.110)  |

### Network delay

| Node | PING |
| -------- | -------- |
| client --> mosn(11.166.190.224) | 1.356ms |
| mosn(11.166.190.224) --> mosn(11.166.136.110) | 0.097 ms |

### Request mode

| Request content |
| -------- |
| 1K req/resp  |

## Layer-7 proxy

| Scenario | QPS | RT (ms) | MEM (K) | CPU (%) |
| -------- | -------- | -------- | -------- | -------- |
| Bolt       | 16000     |15.8     | 77184     |98     |
| Http/1.1   | 4610     |67    | 47336     |90     |
| Http/2     | 5219     |81     | 31244     |74     |


# Multi-core performance (gateway)
## Test environment

### Machine information

| Machine | OS | CPU |
| -------- | -------- | -------- |
| 11.166.190.224 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2640 v3 @ 2.60GHz |
| 11.166.136.110 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz |
| bolt client |  The client serves as the load platform which consists of 5 load generators. It is estimated that there may be 500 connections between the client and client MOSN.  |
| http1 client(10.210.168.5) | ApacheBench/2.3  | -n 2000000 -c 500 -k |
| http2 client(10.210.168.5) | nghttp.h2load  | -n1000000 -c5 -m100 -t4 |

### Deployment structure

| Test mode | Deployment structure |
| -------- | -------- |
| Direct connected     | client --> mosn(11.166.190.224) --> server(11.166.136.110)  |

### Network delay

| Node | PING |
| -------- | -------- |
| client --> mosn(11.166.190.224) | 1.356ms |
| mosn(11.166.190.224) --> mosn(11.166.136.110) | 0.097 ms |

### Request mode

| Request content |
| -------- |
| 1K req/resp  |

## Layer-7 proxy

| Scenario | QPS | RT(ms) | MEM(K) | CPU(%) |
| -------- | -------- | -------- | -------- | -------- |
| Bolt       | 45000     |23.4     | 544732     |380     |
| Http/1.1   | 21584     |23     | 42768    |380     |
| Http/2     | 8180     |51.7     | 173180     |300     |

# Persistent connection gateway

## Test environment

### Machine information

| Machine | OS | CPU |
| -------- | -------- | -------- |
| 11.166.190.224 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2640 v3 @ 2.60GHz |
| 11.166.136.110 | 3.10.0-327.ali2010.rc7.alios7.x86_64 | Intel(R) Xeon(R) CPU E5-2430 0 @ 2.20GHz |

### Deployment structure

| Test mode | Deployment structure |
| -------- | -------- |
| Direct connected     | client --> mosn(11.166.190.224) --> server(11.166.136.110)  |

### Network delay

| Node | PING |
| -------- | -------- |
| client --> mosn(11.166.190.224) | 1.356ms |
| mosn(11.166.190.224) --> mosn(11.166.136.110) | 0.097 ms |

### Request mode

| Connection counts|Request content |
| -------- | -------- |
| 2 load generators，50,000 connections + 500 QPS for each load generators，100,000 connections + 1000 QPS in total | 1K req/resp  |

## Persistent connection gateway

| Scenario | QPS | MEM (g) | CPU (%) | goroutine |
| -------- | -------- | -------- | -------- | -------- |
| RWLoop + goroutine | 1000    | 3.3     |60     | 200028 |
| Raw epoll   | 1000     | 2.5   |  18  | 28 |


#  Conclusion

MOSN 0.2.1 has introduced the memory reuse framework. Compared with MOSN 0.1.0, MOSN 0.2.1 has great performance improvement in the scenario of bolt protocol forwarding, with QPS increased by **20%** and memory usage saved by **30%**.

At the same time, we have also conducted preliminary performance tests respectively in the scenarios of HTTP/1.1 and HTTP/2. So far, the performance indifferent. This is mainly because the IO and Stream of HTTP protocol family are processed by the third-party library, with poor integration with the existing processing framework of MOSN. We will perform optimizations in subsequent iterations to improve the performance of the MOSN in processing HTTP protocol family.

In addition, for the scenario where there are a large number of links (such as persistent connection gateway), SOFAMson has introduced the Raw Epoll + goroutine pool mode to cope with sharp goroutine increase, thus greatly optimizing the QPS and memory performance in such scenario.


# Appendix

## Version comparison

Page size 0~10k, 5k in average
1000 downstream connections; 6 upstream connections
Single-core pressure test

| __Version__ | __QPS__ | Memory |
| --- | --- | --- |
| 0.1.0 | 10500 | 175M |
| 0.2.1 | 13000 | 122M |

