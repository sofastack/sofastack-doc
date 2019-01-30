# Pilot introduction

The SOFAMesh project forked the Istio project to enhance Pilot's capabilities. Currently, the ongoing enhancements are focused on the following three areas:

- Support ZooKeeper as a registry center, and support SOFA, DUBBO and other microservice frameworks using ZooKeeper as a registry center.
- Support the common protocol framework. Use a common protocol, and support multiple protocols simultaneously based on Kubernetes DNS.
- Add register agent to support the container models of SOFA, Dubbo and HSF. Namely, a single application can register multiple service instances.