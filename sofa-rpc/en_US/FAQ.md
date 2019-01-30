# FAQ

## Common issues

### Q: Is SOFARPC the version used inside Ant Financial?
Yes, SOFARPC has excellent extension interfaces, and the version for internal use just has some additional extension implementations based on the open source version. For example, the cloud-based commercial version integrates the Ant Financial Technology's shared registry center, Distributed System Tracing (DST) and other products. The version for internal use integrates Ant Financial's internal registry center, LDC router and other individual extensions.


### Q: Does SOFARPC use ZooKeeper as the registry center internally? Can it integrate other registry centers such as etcd?
Ant Financial uses its self-developed registry products internally. SOFARPC's registry modules are extensible. All the registry modules use the same set of core interfaces for both internal and external use. Currently, the open-source version has integrated with ZooKeeper, and other registry implementation communities are being integrated.

### Q: What is the difference between SOFARPC and Dubbo?
Dubbo, developed by Alibaba Group, is an excellent open-source RPC framework featuring high performance and good scalability. Dubbo is a comparatively mature open source framework, with a large number of users and rich open source ecology. Now, it has joined the Apache Foundation for incubation. Dubbo was first widely used in the Alibaba B2B department. 

Originated from HSF in Alibaba Group, SOFARPC now has grown to an independent product. SOFARPC has carried out a lot of reconstruction and optimization on the aspects of protocol, network, routing, and scalability to meet the large-scale financial business scenarios of Ant Financial. In the Ant Financial's middleware (SOFAStack) ecosystem, SOFARPC is supported by a comprehensive microservices technology stack, including Microservices R&D framework, RPC framework, service registry center, distributed scheduling task, throttling framework, Dynamic Configuration, DST, Metrics  and others. By Dec. 11, 2017, SOFARPC has been used by thousands of systems in Ant Financial, and the production environment has released more than tens of thousands of interfaces.

However, in the open source field, SOFARPC is still at the initial stage, and its open source ecosystem is still under construction. With the advancement of the open source plan, various related components will be available in the subsequent versions to improve the microservices stack. You are welcome to build SOFAStack together with us.

In terms of performance, the technical points of both products involved in protocols are similar. As for scalability, both products have good scalability.
As for other functional differences, here are some features that have been opened source or will be open sourced in the near future for reference: 

- SOFARPC supports HTTP/2 and GRPC protocols and provides such capabilities as service warm-up weight, automatic degradation upon fault, negotiation mechanism, and CRC data validation. 
- In combination with SOFABoot, SOFARPC can separate the RPC framework from business classes to prevent class conflicts.
- The cross-IDC SOFARPC routing, which includes the support to remote active-active disaster recovery by cooperating with the service registration system, is very distinctive. 
- The "micro-transaction" architecture implemented by SOFARPC in the process of combining with the internal Microservices to achieve a consistent framework is a very valuable outcome of Ant Financial in the financial industry. This is also the difference between SOFARPC and Dubbo.

### Q: What are the advantages of SOFARPC compared with other RPC frameworks?
As a RPC framework, the most basic ability of SOFARPC is RPC call. Other differences between SOFARPC and other PRC frameworks lie in performance, scalability and functionality. Different RPC frameworks may place their emphasis on different aspects.

The large-scale application of SOFARPC within Ant Financial proves that SOFARPC is a reliable production-grade RPC framework. The financial nature of Ant Financial determines the functional focus of SOFARPC in the financial scenario.

### Q: What is the difference between SOFARPC and Spring Cloud?
Since SOFARPC serves as a RPC framework, SOFARPC and Spring Cloud are incomparable. Spring Cloud is comparable to SOFAStack which is a financial distributed middleware independently developed by Ant Financial. The Spring Cloud is a complete distributed solution and contains various components needed to build a financial cloud-based native architecture, including microservices R&D framework, RPC framework, service registry center, distributed scheduling task, throttling framework, Dynamic Configuration, DST, Metrics, as well as distributed highly-available message queue, distributed transaction framework, distributed database proxy and other components.
Stack will gradually become open source in the future. All the SOFAStack components will be open sourced gradually in the future.



## R&D related issues
### Q: Why doesn't SOFARPC use JDK8?
The scenarios that use JDK6 still exist, so you'd better select JDK7 for compilation while JDK6 for the compilation level.
