# Kubernetes (K8s)

## What is Container Orchestration Platform?
- they manage containers
- it is a self-healing platform
- it is suitable for deploying your application and make them Highly Available
- is normally setup as a Cluster of Nodes/Servers(On-Prem, Virtual Machine, Cloud Machines,etc)
- they can manage any of container runtimes like Docker, Podman, LXC, Rkt, etc,.
- they monitor the health of your application, 
- if your application instance is non-reponsive or it got aborted, then K8s replaces them with 
  new healthy instance in order to fix issues
- you can scale up/down your application deployments
- you can expose your application deployments as Services
- Service are two types broadly
   1. External Service
        Frontend - webpage
        NodePort Service
        LoadBalancer Service
   2. Internal Service
        e.g Database
        ClusterIP
- Examples
    Docker SWARM
	- it is native orchestration platform from Docker which supports only Docker Runtime
    Google Kubernetes
        - opensource
        - supports many types of Container Runtimes
            - Docker
            - LXC
            - Rkt 
            - Podman
            - CRI-O
    RedHat OpenShift (IBM) 
        - Orchestration platform that is built on top of Google Kubernetes
        - it is an Enterpise Product coming from RedHat(an IBM company)
        - you can expect support when things go wrong
        - it provides a private Docker registry out of the box
	- CI/CD with Jenkins, etc., 
        - SLA
	    - Platinum customer
	    - Gold customer
	    - Silver customer

Kubernetes Cluster
   - one or more master node(this can on-prem,vm,cloud servers,etc)
	- kubelet - Kubernetes Agent (Background Service)
		- this is the one which communicates with the Container Runtime
		- this is the one which monitors the health of your application instances
                  and periodically reports the status back to API Server on the master node
	- Container Runtime has to be installed ( Docker, LXC, Podman, etc,.)
        - control plane components
		- orchestration functionalities are offered by these components
		- API Server (implements all K8s functionality as REST API)
		- etcd - dictionary style datastore used by API server to save cluster status
                - Scheduler - helps in identify a worker machine/node where a new appln instance
                              can be deployed
                - Controller Managers (helps in monitoring, repairing, etc,.)
			- Node Controller
                        - Deployment Controller
			- ReplicationController
			- ReplicaSet Controller
			- Endpoint Controller
   - one or more worker nodes(on-prem, vm, cloud servers, etc)
	- this is where user application workloads(instances) will be deployed
        - kubelet - Kubernetes Agent ( Background Services)
	- Container Runtime has to be installed ( Docker, LXC, Podman, etc,.)

- Kubernetes Tools
	- kubelet
		- service
		- to manage the workloads on the node where this service runs
		- gives a heart-beat kind of update to master node
		- when the updates stops coming from kubelet, master node understand
	          there is some network issue, node is down, etc

	- kubectl - client tool ( REST API calls to API Server )
           - this is the tool that will be used to interact with the K8s cluster
           - helps in deploying your applications in K8s cluster

	- kubeadm - administrative tool
	  - used to setup K8s cluster
          - used to boostrap master node
	  - used to join/unjoin a worker node from K8s cluster

### Setting up minikube K8s cluster ( rps user )
The official page with detailed instruction can be found here https://minikube.sigs.k8s.io/docs/start/

```
cd /home/rps
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Now let's install minikube, the assumption is docker is already installed on system and you are able to 
access Docker commands as non-root user(rps)
```
minikube start --driver=docker
```
The expected output is
<pre>
[jegan@tektutor ~]$ minikube start --driver=docker
😄  minikube v1.24.0 on Centos 8.3.2011
✨  Using the docker driver based on user configuration
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.22.3 preload ...
    > preloaded-images-k8s-v13-v1...: 1.84 MiB / 501.73 MiB [>__] 0.37    > preloaded-images-k8s-v13-v1...: 7.01 MiB / 501.73 MiB [>__] 1.40    > preloaded-images-k8s-v13-v1...: 11.83 MiB / 501.73 MiB [>_] 2.36    > preloaded-images-k8s-v13-v1...: 16.61 MiB / 501.73 MiB  3.31% 24    > gcr.io/k8s-minikube/kicbase: 0 B [___________________________] ?    > preloaded-images-k8s-v13-v1...: 21.42 MiB / 501.73 MiB  4.27% 24    > gcr.io/k8s-minikube/kicbase: 24.36 KiB / 355.78 MiB [>____] 0.01    > preloaded-images-k8s-v13-v1...: 26.17 MiB / 501.73 MiB  5.22% 24    > gcr.io/k8s-minikube/kicbase: 24.36 KiB / 355.78 MiB [>____] 0.01    > preloaded-images-k8s-v13-v1...: 30.80 MiB / 501.73 MiB  6.14% 24    > gcr.io/k8s-minikube/kicbase: 24.36 KiB / 355.78 MiB  0.01% 40.59    > preloaded-images-k8s-v13-v1...: 35.55 MiB / 501.73 MiB  7.08% 24    > gcr.io/k8s-minikube/kicbase: 24.36 KiB / 355.78 MiB  0.01% 40.59    > preloaded-images-k8s-v13-v1...: 40.45 MiB / 501.73 MiB  8.06% 24    > gcr.io/k8s-minikube/kicbase: 183.85 KiB / 355.78 MiB  0.05% 40.5    > preloaded-images-k8s-v13-v1...: 45.08 MiB / 501.73 MiB  8.98% 24    > gcr.io/k8s-minikube/kicbase: 1.20 MiB / 355.78 MiB  0.34% 167.01    > preloaded-images-k8s-v13-v1...: 48.83 MiB / 501.73 MiB  9.73% 24    > gcr.io/k8s-minikube/kicbase: 2.51 MiB / 355.78 MiB  0.70% 167.01    > preloaded-images-k8s-v13-v1...: 52.23 MiB / 501.73 MiB  10.41% 2    > gcr.io/k8s-minikube/kicbase: 4.04 MiB / 355.78 MiB  1.14% 167.01    >     > preloaded-images-k8s-v13-v1...: 5    > preloaded-images-k8s-v13-v1...: 501.73 MiB / 501.73 MiB  100.00% 19.17 Mi
    > gcr.io/k8s-minikube/kicbase: 355.78 MiB / 355.78 MiB  100.00% 7.84 MiB p/
🔥  Creating docker container (CPUs=2, Memory=3900MB) ...
🐳  Preparing Kubernetes v1.22.3 on Docker 20.10.8 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
💡  kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
</pre>

### Troubleshooting

In case you are getting permission denied error when you attempt this as rps user, then try this
```
newgrp docker
docker images
```
