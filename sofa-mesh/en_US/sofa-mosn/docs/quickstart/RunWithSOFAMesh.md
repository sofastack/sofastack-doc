# Use SOFAMosn to build Service Mesh platform

This article introduces how to use SOFAMosn to build the Service Mesh development environment based on SOFAMesh framework, and verify some basic capabilities of SOFAMosn, such as routing and load balancing. This article includes the following content:

+ Relationship between SOFAMosn and SOFAMesh
+ Preparations
+ Deploy SOFAMesh with source codes
+ Bookinfo experiment

## Relationship between SOFAMosn and SOFAMesh

As mentioned in [SOFAMosn introduction](../Introduction.md), SOFAMosn is a Service Mesh data plane agent developed with Golang, and SOFAMesh is a large-scale implementation solution for Service Mesh, which is improved and extended based on Istio. Serving as a critical component of SOFAMesh, SOFAMosn is used to complete data plane forwarding.


The following figure shows the workflow chart of SOFAMosn based on the overall SOFAMesh framework. <br>
[Note: Currently, SOFAMosn cannot be directly used in the native Istio].


<div align=center><img src="../images/MOSNIntroduction.png" width = "450" height = "400" /></div>



## Preparations

This guide supposes you are using macOS. For other operating systems, you can install the corresponding software.

### 1. Install HyperKit

Install [docker-for-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac), and then  [install driver](https://github.com/kubernetes/minikube/blob/master/docs/drivers.md#hyperkit-driver).

#### 1.1 Install Docker 

Download the Docker software package to install it or run the following command to install it:

```bash
$ brew cask install docker
```

#### 1.2 Install driver

```bash
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/docker-machine-driver-hyperkit \
&& chmod +x docker-machine-driver-hyperkit \
&& sudo mv docker-machine-driver-hyperkit /usr/local/bin/ \
&& sudo chown root:wheel /usr/local/bin/docker-machine-driver-hyperkit \
&& sudo chmod u+s /usr/local/bin/docker-machine-driver-hyperkit
```

### 2. Install Minikube (or purchase the commercial version of k8s cluster)

It is recommended to use Minikube V0.28 or later, see  [https://github.com/kubernetes/minikube](https://github.com/kubernetes/minikube).

```bash
$ brew cask install minikube
```

### 3. Start Minikube

Note that Pilot requires at least 2G memory, so you can add resources to Minikube by adding parameters at startup. If your machine has insufficient resources, it is recommended to use the commercial version of the Kubernetes cluster.

```bash
$ minikube start --memory=8192 --cpus=4 --kubernetes-version=v1.10.0 --vm-driver=hyperkit
```

### 4. Install kubectl command line tool

kubectl is a command line interface used to run commands for Kubernetes cluster. For how to install it, see [https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl).

```bash
$ brew install kubernetes-cli
```

### 5. Install Helm

Helm is a package management tool for Kubernetes. For how to install it, see [https://docs.helm.sh/using\_helm/#installing-helm](https://docs.helm.sh/using_helm/#installing-helm).

```bash
$ brew install kubernetes-helm
```

## Deploy SOFAMesh with source codes

### 1. Download SOFAMesh source codes

```bash
$ git clone git@github.com:alipay/sofa-mesh.git
$ cd sofa-mesh
```

### 2. Use Helm to install SOFAMesh

Before you use Helm to install SOFAMesh, you must check the version of Helm.

```bash
$ helm version
```

If the version is earlier than 2.10, you need to manually install the CRD of Istio, otherwise you don't have to do it. In this guide, the installed Helm is V2.11.0, so the following steps can be skipped directly.

```bash
$ kubectl apply -f install/kubernetes/helm/istio/templates/crds.yaml
$ kubectl apply -f install/kubernetes/helm/istio/charts/certmanager/templates/crds.yaml
```

There are two methods to use Helm to install Istio. The first one is recommended. If the first one does not work, try the second one.

+ Method 1: Use `helm template` to install

    ```bash
    $ helm template install/kubernetes/helm/istio --name istio --namespace istio-system > $HOME/istio.yaml
    $ kubectl create namespace istio-system
    $ kubectl apply -f $HOME/istio.yaml
    ```

    If you want to uninstall the installed Istio, run the following command:

    ```bash
    $ kubectl delete -f $HOME/istio.yaml
    ```

+ Method 2: Use `helm install` to install

    ```bash
    $ kubectl apply -f install/kubernetes/helm/helm-service-account.yaml
    $ helm init --service-account tiller
    $ helm install install/kubernetes/helm/istio --name istio --namespace istio-system
    ```

    If you want to uninstall Istio, run the following command:

       ```bash
    $ helm delete --purge istio
       ```

### 3. Verify installation

```bash
$ kubectl get svc -n istio-system
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                 AGE
istio-citadel              ClusterIP   172.16.113.0     <none>        8060/TCP,9093/TCP                       2m
istio-egressgateway        ClusterIP   172.16.93.234    <none>        80/TCP,443/TCP                          2m
istio-galley               ClusterIP   172.16.199.113   <none>        443/TCP,9093/TCP                        2m
istio-pilot                ClusterIP   172.16.94.105    <none>        15010/TCP,15011/TCP,8080/TCP,9093/TCP   2m
istio-policy               ClusterIP   172.16.152.158   <none>        9091/TCP,15004/TCP,9093/TCP             2m
istio-sidecar-injector     ClusterIP   172.16.226.86    <none>        443/TCP                                 2m
istio-statsd-prom-bridge   ClusterIP   172.16.18.241    <none>        9102/TCP,9125/UDP                       2m
istio-telemetry            ClusterIP   172.16.200.109   <none>        9091/TCP,15004/TCP,9093/TCP,42422/TCP   2m
prometheus                 ClusterIP   172.16.157.229   <none>        9090/TCP                                2m
```

When the pods in the istio-system namespace are Running,  it means SOFAMesh has been successfully deployed.

```bash
$ kubectl get pods -n istio-system
NAME                                       READY    STATUS   RESTARTS    AGE
istio-citadel-6579c78cd9-w57lr              1/1     Running   0          5m
istio-egressgateway-7649f76df4-zs8kw        1/1     Running   0          5m
istio-galley-c77876cb6-nhczq                1/1     Running   0          5m
istio-ingressgateway-5c9c8565d9-d972t       1/1     Running   0          5m
istio-pilot-7485f9fb4b-xsvtm                1/1     Running   0          5m
istio-policy-5766bc84b9-p2wfj               1/1     Running   0          5m
istio-sidecar-injector-7f5f586bc7-2sdx6     1/1     Running   0          5m
istio-statsd-prom-bridge-7f44bb5ddb-stcf6   1/1     Running   0          5m
istio-telemetry-55ff8c77f4-q8d8q            1/1     Running   0          5m
prometheus-84bd4b9796-nq8lg                 1/1     Running   0          5m
```

## BookInfo experiment

BookInfo is a book application like Douban. It contains four basic services.

* Product Page: Homepage, which is developed with Python, shows all book information and calls the Reviews and Details services.
* Reviews: Comment, which is developed with Java, shows book reviews and calls the Ratings service.
* Ratings: Rating service, which is developed with Nodejs.
* Details: Book details, which is developed with Ruby.

<div align=center><img src="../images/bookinfo.png" width = "550" height = "400" /></div>


### 1. Deploy BookInfo application and inject it to SOFAMosn

> For the specific procedure, see [https://istio.io/docs/examples/bookinfo/](https://istio.io/docs/examples/bookinfo/).

+ Inject to SOFAMosn

```bash
$ kubectl label namespace default istio-injection=enabled
```
+ Deploy Bookinfo

```bash
$ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

+ Verify if the deployment is successful

```bash
$ kubectl get services
NAME                       CLUSTER-IP   EXTERNAL-IP   PORT(S)              AGE
details                    10.0.0.31    <none>        9080/TCP             6m
kubernetes                 10.0.0.1     <none>        443/TCP              7d
productpage                10.0.0.120   <none>        9080/TCP             6m
ratings                    10.0.0.15    <none>        9080/TCP             6m
reviews                    10.0.0.170   <none>        9080/TCP             6m
```

+ Wait till all pods run successfully

```bash
$ kubectl get pods
NAME                                        READY     STATUS    RESTARTS   AGE
details-v1-1520924117-48z17                 2/2       Running   0          6m
productpage-v1-560495357-jk1lz              2/2       Running   0          6m
ratings-v1-734492171-rnr5l                  2/2       Running   0          6m
reviews-v1-874083890-f0qf0                  2/2       Running   0          6m
reviews-v2-1343845940-b34q5                 2/2       Running   0          6m
reviews-v3-1813607990-8ch52                 2/2       Running   0          6m
```

### 2. Access BookInfo service

* Enable gateway mode
    ```bash
    $ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
    $ kubectl get gateway        // Check if the gateway runs
    NAME               AGE
    bookinfo-gateway   24m
    ```

* Check if EXTERNAL-IP exists
```bash
$ kubectl get svc istio-ingressgateway -n istio-system
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                     AGE
istio-ingressgateway   LoadBalancer   172.19.8.162   161.117.70.217   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:32393/TCP,8060:30940/TCP,15030:31601/TCP,15031:31392/TCP   48m
```

* Set ingress IP and ports
```bash
$ export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
$ export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
```

* Set gateway address
```bash
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT
$ echo $GATEWAY_URL   //For example, the address here is 161.117.70.217:80
161.117.70.217:80
```

* Verify if the gateway takes effect 
```bash
$ curl -o /dev/null -s -w "%{http_code}\n"  http://$GATEWAY_URL/productpage   //If 200 is output, it means a success 
200
```

* Observe the page status


Visit `http://$GATEWAY_URL/productpage`. Note that, you need to replace `$GATEWAY_URL` with the address you set. Normally, you can see the following BookInfo interface after refreshing the page. There are three versions of Book Reviews. After refreshing, you can see them in turn. To learn why these three versions appear, view the configuration in `samples/bookinfo/platform/kube/bookinfo.yaml`.

+ Interface of version 1
![](../images/v1.png)

+ Interface of version 2
![](../images/v2.png)

+ Interface of version 3
![](../images/v3.png)

### 3. Verify MOSN's capability of routing by version

+ Firstly, create a series of destination rules for BookInfo's services.

    ```bash
    $ kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml
    ```

+ Specify reviews service to access v1 only.

```bash
$ kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml
```

Visit http://$GATEWAY_URL/productpage, and find that the reviews are fixed in the following v1 page and no longer change.

![](../images/v1.png)

### 4. Verify MOSN's capability of routing by weight

+ Allocate 50% traffic respectively to v1 and v3.
    ```bash
    $ kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-50-v3.yaml
    ```

Access http://$GATEWAY_URL/productpage, and find that the probability for v1 and v3 to appear respectively is 1/2.

### 5. Verify MOSN's capability of routing by specific header

+ There is a login entrance in the upper right corner of the BookInfo system. After you log in, the request carries the custom parameter `end-user` of which the value is user name. Mosn supports routing by the value of this header. For example, route the user Jason to the v2 while other users to v1 (both username and password are Jason, why this user can view the corresponding yaml file).

    ```bash
    $ kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-test-v2.yaml
    ```

When you access http://$GATEWAY_URL/productpage:

+ Log in with the account of Jason and you will see the v2 interface.
![](../images/login.png)

+ Log in with the account of another user, you are always on v1 interface.
![](../images/v1.png)
