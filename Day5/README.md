# Openshift

## Overview
- Openshift is developed on top of Google Kubernetes
- To work in Openshift one has to be comfortable working in Containers and Kubernetes
- OpenShift is a RedHat Product(an IBM company)
- OpenShift comes as IaaS, PaaS and SaaS
- OpenShift supports a Private Container Registry out of the box
- OpenShift supports setup Gogs - GitHub like Private version control within OpenShift
- OpenShift supports setting up CI/CD Pipeline in addition to all Kubernetes functionalities
- OpenShift CI/CD pipeline can involve Jenkins, JFrog Artifactory/Sonatype Nexus, SonarQube, 
  Dev,QA,Stage, Prod environments all running as Pods within OpenShift
- OpenShift use a tool called 'oc' in the place of 'kubectl'
- In OpenShift, one has a create a project first in order to deploy applications
- OpenShift project is nothing but a namespace, hence project names must be unique within OpenShift


### Using RedHat OpenShift Sandbox v4.9 CLI from master VM

Download openshift client tool
```
https://access.redhat.com/downloads/content/290/ver=4.9/rhel---8/4.9.10/x86_64/product-software
```

You need to download the OpenShift v4.9 Linux Client from the above URL.

Extract the zip file
```
cd /home/rps/Downloads
tar xvfz oc-4.9.10-linux.tar.gz
sudo mv kubectl /usr/local/bin
sudo mv oc /usr/local/bin
cd /home/rps
```

### Applying Docker image pull secrets in OpenShift
```
oc create secret docker-registry dockerhub-registry-secrets --docker-server=https://index.docker.io/v1 \
   --docker-username=<your-docker-username> --docker-password=<your-dockerhub-password> \
   --docker-email=<your-dockerhub-email>

oc secrets link default dockerhub-registry-secrets --for=pull
```


### Deploying your application into OpenShift RedHat Sandbox
1. Login as a Developer
2. Click on Add menu on the left side
3. Select Import from Git
4. In the Git URL field, paste the URL https://github.com/tektutor/spring-ms.git
5. Create Button

This will create build, as part of the build once the code is compiled, it prepares a Podman image, pushes the image to OpenShift's Private ImageStream Registry. From the newly pushed image in ImageStream it deploys your application with a single Pod. OpenShift also creates a service for your deployment and it exposes a route. Route is a publicly accessible URL.

### Finding if jenkins templates is available in openshift
```
oc get templates -n openshift | grep jenkins
```

### Let's create a new application jenkins-ephemeral
```
oc new-app jenkins-ephemeral
```

### Retrieve the jenkins route(public url)
```
oc get routes
```

### In case you are curious to look inside the template
```
oc get template/jenkins-ephemeral -o json -n openshift
```

### Let's create a buildconfig to setup nodejs pipeline
```
oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/jenkins/pipeline/nodejs-sample-pipeline.yaml
```

### You may see the pipeline created
```
oc get buildconfig/nodejs-sample-pipeline -o yaml
```

### See the buildconfigs
```
oc get buildconfigs
```

### Let's start the build now
```
oc start-build nodejs-sample-pipeline
```
Now watch the activities in the Jenkins !

### If all goes well, you should be able to see a route for nodejs application
```
oc get routes
```
You can copy & paste the route url to see the nodejs webpage.


You may copy the jenkins route url and access jenkins from the web browser

## Installing OpenShift in CentOS 7.7
- We will install OpenShift v3.11 in the CentOS 7.7 Virtual Machine which is blank with no softwares
  installed at this point.

### Installing Docker Community Edition as rps user
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y  docker-ce docker-ce-cli containerd.io
```

Adding rps user to docker group
```
sudo usermod -aG docker $USER
newgrp docker
```

Creating an insecure Docker Registry for OpenShift internal use
```
sudo mkdir /etc/docker /etc/containers

sudo tee /etc/containers/registries.conf<<EOF
[registries.insecure]
registries = ['172.30.0.0/16']
EOF

sudo tee /etc/docker/daemon.json<<EOF
{
   "insecure-registries": [
     "172.30.0.0/16"
   ]
}
EOF
```

Restart Docker to apply config changes
```
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
```

Enable IP Forwarding
```
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

Configure Firewall ports for OpenShift
```
DOCKER_BRIDGE=`docker network inspect -f "{{range .IPAM.Config }}{{ .Subnet }}{{end}}" bridge`
sudo firewall-cmd --permanent --new-zone dockerc
sudo firewall-cmd --permanent --zone dockerc --add-source $DOCKER_BRIDGE
sudo firewall-cmd --permanent --zone dockerc --add-port={80,443,8443}/tcp
sudo firewall-cmd --permanent --zone dockerc --add-port={53,8053}/udp
sudo firewall-cmd --reload
```

Install OpenShift binaries
```
wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
tar xvf openshift-origin-client-tools*.tar.gz
cd openshift-origin-client*/
sudo mv  oc kubectl  /usr/local/bin/
```

Verify OpenShift version
```
oc version
```

Restart Docker to apply config changes
```
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
```

Start our OpenShift Cluster
```
oc cluster up
```

Troubleshooting oc cluster up failures
```
oc cluster down
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
oc cluster up
```

Login to OpenShift console via CLI as administrator
```
oc login -u system:admin
```

Listing nodes in Openshift Cluster
```
oc get nodes -o wide
```

Checking status
```
oc status
```

Checking currently logged in user
```
oc whoami
```

Login to OpenShift console via CLI as developer
```
oc login -u developer:developer
```

Accessing web console
```
https://127.0.0.1:8443/console
```


### Finding OpenShift Docker Registry details
```
oc login -u system:admin
oc get svc --all-namespaces
```
The expected output is
<pre>
[jegan@localhost openshift-nov-2021]$ oc get svc --all-namespaces
NAMESPACE                       NAME                          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                   AGE
<b>default                         docker-registry               ClusterIP   172.30.1.1       <none>        5000/TCP</b>                  11h
default                         kubernetes                    ClusterIP   172.30.0.1       <none>        443/TCP                   11h
default                         router                        ClusterIP   172.30.227.176   <none>        80/TCP,443/TCP,1936/TCP   11h
kube-dns                        kube-dns                      ClusterIP   172.30.0.2       <none>        53/UDP,53/TCP             11h
openshift-apiserver             api                           ClusterIP   172.30.50.226    <none>        443/TCP                   11h
openshift-service-cert-signer   service-serving-cert-signer   ClusterIP   172.30.192.20    <none>        443/TCP                   11h
openshift-web-console           webconsole                    ClusterIP   172.30.236.73    <none>        443/TCP                   11h
</pre>

### Pushing Docker Hub Images into OpenShift Private Docker Registry
```
oc login -u developer:developer
oc new-project tektutor
docker login -u openshift -p $(oc whoami -t) 172.30.1.1:5000
docker pull docker.io/nginx:1.20
docker tag docker.io/nginx:1.20 172.30.1.1:5000/tektutor/nginx:1.20
docker push 172.30.1.1:5000/tektutor/nginx:1.20
```
