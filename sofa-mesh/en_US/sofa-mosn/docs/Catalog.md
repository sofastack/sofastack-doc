# Documents related to MOSN
## [MOSN introduction](./Introduction.md)

## Get started with MOSN
+ Compile MOSN
+ Run MOSN sample code
+ Learn and modify MOSN configuration file
+ Use MOSN Docker Image
+ [Build a Service Mesh Platform with MOSN](quickstart/RunWithSOFAMesh.md)

## Analyze MOSN source code (API instruction)
### Analyze source code on network/IO layer
+ Analyze MOSN Listener management implementation mechanism
+ Analyze MOSN Connection management implementation mechanism 
+ Analyze MOSN IO read implementation mechanism
+ Analyze MOSN TLS implementation mechanism
    + HTTP/1.x on TLS implementation
    + HTTP/2 on TLS implementation
    + SOFARPC on TLS implementation
    
### Analyze source code on protocol layer
+ Analyze the implementation of protocol extension  mechanism
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
+ Analyze MOSN Proxy implementation
    + TCP Proxy implementation
    + 7-layer Proxy implementation
    
+ Analyze MOSN routing implementation
    + Virtual Host and specific routing matching implementation mechanism 
    + subset matching implementation
    + Retry mechanism
    
+ Analyze the implementation of MOSN cluster management and load balancing
    + Connection pool implementation mechanism
    + Heartbeat implementation mechanism 
    + Load balancing implementation mechanism

### MOSN advanced features and optimization
+ Analyze MOSN process management implementation mechanism 
    + MOSN smooth upgrade
    + MOSN smooth reload
+ Analyze MOSN GC optimization and memory reuse implementation mechanism
+ Analyze MOSN thread model

## Pressure test reports
+ [0.1.0 pressure test report](./reference/PerformanceReport010.md)
+ [0.2.1 pressure test report](./reference/PerformanceReport021.md)

## FAQ
+ How is MOSN?
+ What is the use condition of MOSN in Ant Financial?
+ What is the roadmap for MOSN?
+ Others
