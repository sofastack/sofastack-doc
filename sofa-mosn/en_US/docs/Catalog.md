# Documents related to MOSN
## [MOSN introduction](./Introduction.md)

## Get started with MOSN
+ Compile MOSN
+ Run MOSN sample code
+ Learn and modify MOSN configuration file
+ Use MOSN Docker Image
+ [Build a Service Mesh Platform with MOSN](quickstart/RunWithSOFAMesh.md)

## Analyze MOSN‘s source code (API instruction)
### Analyze source code on network/IO layer
+ Analyze MOSN's Listener management implementation mechanism
+ Analyze MOSN's Connection management implementation mechanism 
+ Analyze MOSN's IO read implementation mechanism
+ Analyze MOSN's TLS implementation mechanism
    + HTTP/1.x on TLS implementation
    + HTTP/2 on TLS implementation
    + SOFARPC on TLS implementation
    
### Analyze source code on protocol layer
+ Analyze the implementation of protocol extension mechanism
+ Analyze the implementation of MOSN multi-protocol mechanism
    + SOFARPC protocol implementation
    + HTTP1.1 implementation 
    + HTTP2.0 implementation 
    + Dubbo implementation 
    
### Analyze source code on stream layer
+ Analyze Stream management implementation mechanism
+ Analyze Stream connection pool implementation mechanism
+ Analyze Stream Filter extension mechanism

### Analyze source code on proxy layer
+ Analyze MOSN's Proxy implementation
    + TCP Proxy implementation
    + Layer-7 Proxy implementation
    
+ Analyze MOSN's routing implementation
    + Virtual Host and specific routing matching implementation mechanism 
    + Subset matching implementation
    + Routing retry mechanism
    
+ Analyze the implementation of MOSN's cluster management and load balancing
    + Connection pool implementation mechanism
    + Heartbeat implementation mechanism 
    + Load balancing implementation mechanism

### MOSN's advanced features and optimization
+ Analyze MOSN's process management implementation mechanism 
    + Smooth upgrade
    + Smooth reload
+ Analyze MOSN's GC optimization and memory reuse implementation mechanism
+ Analyze MOSN's thread model

## Pressure test reports
+ [0.1.0 pressure test report](./reference/PerformanceReport010.md)
+ [0.2.1 pressure test report](./reference/PerformanceReport021.md)

## FAQ
+ How is MOSN?
+ What is the use condition of MOSN in Ant Financial?
+ What is the roadmap for MOSN?
+ Others
