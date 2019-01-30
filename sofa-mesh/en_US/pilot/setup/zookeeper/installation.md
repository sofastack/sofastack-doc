# Installation guide
To use Istio in a non-Kubernetes environment, you must complete the following critical tasks first:

1. Configure the Istio API server for the Istio control plane. You can also use MemStore to launch Pilot for demonstration purpose.
2. Manually add SOFAMosn to all microservice instances and start in SideCar mode.
3. Make sure that all requests are routed through SOFAMosn.

## Set control plane

The Istio control plane consists of four main services: Pilot, Mixter, Citadel, and API server.

### API server

Istio's API server, which is based on Kubernetes API server, provides configuration management and role-based access control. The API server requires an etcd cluster as the underlying persistent storage.

#### Install locally

Use the following Docker compose file to install an API server for POC:

```YAML
version: '2'
services:
  etcd:
    image: quay.io/coreos/etcd:latest
    networks:
      default:
        aliases:
          - etcd
    ports:
      - "4001:4001"
      - "2380:2380"
      - "2379:2379"
    environment:
      - SERVICE_IGNORE=1
    command: [
              "/usr/local/bin/etcd",
              "-advertise-client-urls=http://0.0.0.0:2379",
              "-listen-client-urls=http://0.0.0.0:2379"
             ]

  istio-apiserver:
    image: gcr.io/google_containers/kube-apiserver-amd64:v1.7.3
    networks:
      default:
        aliases:
          - apiserver
    ports:
      - "8080:8080"
    privileged: true
    environment:
      - SERVICE_IGNORE=1
    command: [
               "kube-apiserver", "--etcd-servers", "http://etcd:2379",
               "--service-cluster-ip-range", "10.99.0.0/16",
               "--insecure-port", "8080",
               "-v", "2",
               "--insecure-bind-address", "0.0.0.0"
             ]
```

### Other control plane components

Currently, SOFAMosn hasn't integrated with the components other than Pilot, so you don't need to install Mixer, Citadel and other components.

### Add SOFAMosn Sidecar to microservice instances

Every microservice application instance must have an associated SOFAMosn instance.
