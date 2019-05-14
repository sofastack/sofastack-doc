# Roadmap


![[Roadmap]()](./resources/roadmap.png)

## Task list

Some of the existing internal features will be available in subsequent iterations.

The features that have been implemented are listed in the following table. You are welcome to claim the tasks and [make contributions](https://github.com/sofastack/sofa-rpc/wiki/Contributing).

|Task type |Task |Degree of difficulty|Claimant and time|Planned completion time|Progress|Related issues| 
|:----|:-----------|:-----:|:--------|:--------|:----|:--------:|
| Documentation | Document translation | Low | | | | |
| Code | Flexible persistent connection management | Low | | | | [#56](https://github.com/sofastack/sofa-rpc/issues/56) |
| Code | etcd registry center implementation | Medium | [@wynn5a](https://github.com/wynn5a)<br>2018-6 | | | [#153](https://github.com/sofastack/sofa -rpc/issues/153) | |
| Code | eureka registry center implementation | Medium| [@liufeiit](https://github.com/liufeiit)<br>2018-4 | | | [#52](https://github.com/sofastack/sofa -rpc/issues/52) |
| Code | gRPC support | High | | | | [#57](https://github.com/sofastack/sofa-rpc/issues/57) |
| Code | CXF protocol | High | | | | [#58](https://github.com/sofastack/sofa-rpc/issues/58) |
| Code | TLS support | High | | | | |

## Version iteration Plan

### v5.5.0

- Support JSON serialization
- Support H2 TLS
- Implement flexible connection pool
- Integrate Hystrix
- Support Consul registry center


### v5.6.0

- Support GRPC communication layer
- Support etcd registry center
- Support SOFAMesh
- Implement BOLT version negotiation and CRC verification


### v5.7.0

- Support Telnet built-in instructions
- Support SpringBoot 2.0
- Support Mock function
- Support encryption

### v5.8.0

- Support authorization
- Support SofaRegistry
- Support Reactive