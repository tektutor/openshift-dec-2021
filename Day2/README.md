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

