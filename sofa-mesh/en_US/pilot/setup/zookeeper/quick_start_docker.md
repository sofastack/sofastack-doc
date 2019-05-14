# Use Docker to get started with Istio

This article describes how to quickly start installing and configuring Istio by using Docker Compose.

SOFAMosn can not only support the standard Istio deployment mode, but also support the unilateral Inbound Sidecar or Outbound Sidecar deployment mode to meet the various requirements of users.

## Prerequisites

- Docker
- Docker Compose

## Install Istio

1. Download the latest [release package](https://github.com/sofastack/sofa-mosn/releases).
2. Unzip the installation file and go to the decompressed path. The installation path contains:
    - Sample application path `samples/`.
    - The istioctl client executable file which is in the `/bin` path. The istioctl can be used to create routing rules and policies.
    - Configuration file `istion.VERSION`.
3. Add the Istio's `bin` path to your system's `PATH`. For example, execute the following command in the MacOS or Linux operating system:<br>

    ```SHELL
    export PATH=$PWD/bin;$PATH
    ```
4. Pull up the Istio control plane container:<br>
    ```SHELL
    docker-compose -f install/zookeeper/istio.yaml up -d
    ```
5. Ensure that all Docker containers are running:<br>
   ```SHELL
   docker ps -a
   ```
   <br> If the Istio pilot container terminates unexpectedly, you can run the `istioctl context-create` command and re-execute the previous command.
6. Configure istioctl to use the Istio API server:<br>
    ```bash
    istioctl context-create -context istio-local --api-server
    ```

## Deploy application

Now, you can start deploying the SOFABoot demo program. The demo program includes a client and a server, which communicate with each other through Bolt protocol.

```bash
docker-compose up -f sofa-sample-spec.yaml up -d
```

## Uninstall Istio

```bash
docker-compose up -f install/zookeeper/istio.yaml down
```
