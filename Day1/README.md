### Adding rps user to sudoers file from terminal

Edit /etc/sudoers and add rps user as shown below
```
su -
vim /etc/sudoers
```
You need to add the below line just below line no 100
<pre>
rps ALL=(ALL) ALL
</pre>

Also you need to uncomment line no 111 as shown below
<pre>
%wheel  ALL=(ALL) NOPASSWD: ALL
</pre>

You need to save and exit by pressing Ctrl

### Installing git in CentOS 
```
sudo yum install -y git
```

### What is Hypervisor?
- a general term that refers to virtualization
- virtualization technology
- virtualization technology let's you boot many Operation Systems side by side in the same system simultaneously
- software + software solution
- Processor
  AMD - whichever processor supports AMD-V feature, they all support Virtualization
  Intel - whichever processor supports VT-X feature, they all support Virtualization
- Virtualization technology is heavy-weight
    - become every Virtual machine(aka Guest OS) required dedicated hardware resources
        - CPU
        - RAM
        - Storage, etc
- revolutioned the IT industry
- this technology allows Server consolidation
- 1 Server/workstation can consolidate many servers
- Type 1 Hypervisor and Type 2 Hypervisor
- Type 1 - Bare Metal Hypervisor - used in workstations/Servers ( They don't need an Operating System )
- Type 2 - Used in Laptop/Desktops ( They need a Host OS - Hypervisor is installed on top of Host OS )
- Virtual Machine hosts a fully functional Operating System

Processor with 512 Physical Cores
256 TB RAM
500 TB Storage

How many Virtual Machine(Guest OS) you can deploy and run them at the same time?

512 Virtual Machine - assuming 1 Physical CPU core assigned per VM

Hyperthreading (AMD equivalent is called SMT )
	- each processor support two virtual cores per physical core
	- latest - each physical processor supports upto 4 virtual cores

512 * 4 = 2048 Virtual Machines

2048 * 100 = 204800 Virtual Machines


### Containers
- lightweight application virtualization technology
     - they don't need dedicated hardware resources
     - all the containers and other application processes running on the same
       machine shares the hardware resources available on the underlying host OS.
- this is not an Operation System
- container is an application process that runs in its own network namespace
- each container runs in own namespace i.e virtual sandbox environment
- containers running in separate namespaces they won't be able to damage each other
   - a fault application running in one container will not be able to mess up with the other container memory, filesystem, etc
     just like how virtualization technology isolates Virtual Machines
- only one application should run per container unlike VMs

- Linux Kernels 
  1. Namespace is used to isolate one container from the other
  2. Control Groups - used to put some hardware resource quota restrics for containers

- Container Runtimes
   - LXC - bit low-level
   - Docker - More popular, user-friendly
   - CRI-O
   - Rkt (Rocket)
   - Podman - also popular, near future may be it over the market share of Docker
  
- Docker 
   - is developed by an organization called Docker Inc
   - is developed Go programming Language
   - comes in 2 flavours
       1. Community Edition (CE)
       2. Enterprise Edition (EE)
   - can be installed in Mac (OSX), Linux and Windows
   - When you install Docker in Windows/Mac, you have special installers
      1. Docker for Windows - it comes with a thin-linux layer (Linux Kernel)
      2. Docker for Mac - it comes with a thin-linux layer (Linux Kernel)
   
   - Windows machines
       - can run linux application containers as well as windows application containers
       - supports 2 types of isolation
           - process isolation - similar linux containers ( application process )
           - hyper-v isolation - small footprint Virtual machine which acts like a container

   - Linux machines
       can only run linux application containers and not windows application containers
       
 ### What is a Docker Image ?
 - similar OS ISO images
 - similar VMWare OS Images
 - specficaton of a Docker Container
 - Blueprint of a Docker Container
     - Whatever tools and libraries are required for your application they shall be installed here
 - mysql:8 Docker Image

### What is a Docker Container?
- is an instance of a Docker Image
- it is just an application process not an OS, but it like one
- containers running in the same system, they all share the Host OS kernel
- each container gets its own Network Stack ( 7 OSI Layers )
- each container has its shell
- each container has its filesytem
- each container has its Network Inferface Card(NIC) 
- each container has its own MAC-id for the network card
- with a single image, you could create as many container as you want
- With the mysql:8 docker image, you can create running mysql:8 containers which acquires an Ip address, hostname
  and unique container ID and name
  
- Docker Images is broken down into many layers
- the number of layers varies from one Docker Image to the other
- some Images has just one layer, while others might have many
- Docker uses a Union File System, where many layers combined forms the file system
- Docker Image Layers can be shared/referred by one or more Docker Images
- Docker Image is a JSON file that refers one or Docker Image Layers
- Docker Image/Layers they get a unique ID
- Docker Image ID or the Docker Layer ID is a 256 HASH (SHA-1)
  
### Creating a container
```
docker run -dit --name ubuntu1 --hostname ubuntu1 ubuntu:16.04 /bin/bash
```
The above command will create a new container and runs that.

In the above command 
  - ubuntu1 is the name of the container 
  - ubuntu1 is also the hostname
  - ubuntu:16.04 is the Docker Image
  /bin/bash - application that would be started when the container starts running

- each container gets a unique ID and name
- if you don't assign a name, then Docker Server assigns a random name
- if you don't assign a hostname, then Docker Server assigns the container ID as the hostname for the container

### Installing Docker in CentOS 8
Detailed instruction can be found at official page https://docs.docker.com/engine/install/centos/

```
 sudo yum install -y yum-utils
 sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
 sudo yum install -y docker-ce --allowerasing
```

We need to enable and start the Docker server as a service
```
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
```
Type letter q to come out of status command.

The expected output is
<pre>
[jegan@tektutor openshift-dec-2021]$ sudo systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2021-12-06 01:35:16 PST; 12s ago
     Docs: https://docs.docker.com
 Main PID: 41750 (dockerd)
    Tasks: 8
   Memory: 33.9M
   CGroup: /system.slice/docker.service
           └─41750 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Dec 06 01:35:14 tektutor dockerd[41750]: time="2021-12-06T01:35:14.817136339-08:00" level=warning msg="Your kernel does >
Dec 06 01:35:14 tektutor dockerd[41750]: time="2021-12-06T01:35:14.817168970-08:00" level=warning msg="Your kernel does >
Dec 06 01:35:14 tektutor dockerd[41750]: time="2021-12-06T01:35:14.817307002-08:00" level=info msg="Loading containers: >
Dec 06 01:35:15 tektutor dockerd[41750]: time="2021-12-06T01:35:15.762666865-08:00" level=info msg="Default bridge (dock>
Dec 06 01:35:16 tektutor dockerd[41750]: time="2021-12-06T01:35:16.011870991-08:00" level=info msg="Firewalld: interface>
Dec 06 01:35:16 tektutor dockerd[41750]: time="2021-12-06T01:35:16.217181609-08:00" level=info msg="Loading containers: >
Dec 06 01:35:16 tektutor dockerd[41750]: time="2021-12-06T01:35:16.243763123-08:00" level=info msg="Docker daemon" commi>
Dec 06 01:35:16 tektutor dockerd[41750]: time="2021-12-06T01:35:16.243976877-08:00" level=info msg="Daemon has completed>
Dec 06 01:35:16 tektutor systemd[1]: Started Docker Application Container Engine.
Dec 06 01:35:16 tektutor dockerd[41750]: time="2021-12-06T01:35:16.285333990-08:00" level=info msg="API listen on /var/r>
</pre>

We need to add the rps user into the docker user group in order to gain access to Docker commands
```
id
sudo usermod -aG docker rps
newgrp docker
id
```

You may now verify, if you are able to list Docker images
```
docker images
```

### Downloading docker image from Docker Hub(Remote Registry)

```
docker pull hello-world:latest
```

The expected output is
<pre>
jegan@tektutor openshift-dec-2021]$ <b>docker pull hello-world:latest</b>
latest: Pulling from library/hello-world
2db29710123e: Pull complete 
Digest: sha256:cc15c5b292d8525effc0f89cb299f1804f3a725c8d05e158653a563f15e4f685
Status: Downloaded newer image for hello-world:latest
docker.io/library/hello-world:latest
[jegan@tektutor openshift-dec-2021]$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   2 months ago   13.3kB
</pre>
