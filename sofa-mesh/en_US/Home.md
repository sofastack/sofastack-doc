## SOFAMesh introduction 

## Overview 
SOFAMesh is a large-scale implementation solution for Service Mesh which is improved and extended based on [Istio](https://istio.io). Based on Istio's powerful functions and rich features, SOFAMesh has made the following improvement to meet the performance requirements in large-scale deployment and deal with the actual problems in actual implementation: 

1. Replace Envoy with MOSN that is written with Golang;
2. Merge Mixer into the data plane to address the  performance bottlenecks;
3. Enhance Pilot for a more flexible service discovery mechanism;
4. Add support for SOFARPC and Dubbo.

The following figure shows the architectural differences between SOFAMesh and Istio:

<img alt="SOFAMesh architecture" src="./resources/sofa-mesh-arch.png" width="60%">

## Main components

### SOFAMosn

In SOFAMesh, the data pane adopts Golang to write a module called MOSN (Modular Observable Smart Net-stub), and replaces Envoy with MOSN to integrate with Istio to implement the functions of Sidecar. MOSN is fully compatible with Envoy's APIs.

<img alt="SOFA MOSN architecture" src="./resources/mosn-sofa-mesh-golang-sidecar.png" width="60%">

### SOFA Pilot

SOFAMesh greatly expands and enhances the Pilot module in Istio:

<img alt="SOFA Pilot Architecture" src="./resources/sofa-mesh-pilot.png" width="60%">

1. Add an Adapter for SOFA Registry to provide solutions for super large-scale service registration and discovery;
2. Add data synchronization modules to enable data exchange between multiple service registry centers;
3. Add Open Service Registry API to provide standardized service registration.

Together with Pilot and MOSN, SOFAMesh provides the ability to enable traditional intrusive frameworks (such as Spring Cloud, Dubbo and SOFARPC) and Service Mesh products to communicate with each other, thus it can smoothly evolve and transit to Service Mesh.

## Open source co-construction

The initial version of SOFAMesh was jointly built by Ant Financial and Alibaba Digital Media & Entertainment Group UC Business Group. You are welcome  to participate in the follow-up development and build an open source boutique project together with us.