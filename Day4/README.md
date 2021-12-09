### In case you don't any deployments
```
kubectl create deploy nginx --image=nginx:1.18
kubectl scale deploy/nginx --replicas=3
kubectl run multitool --image=praqma/network-multitool
```

### Using Labels as Selectors
Check all the pods running in your K8s cluster
```
kubectl get po
```

Now let's list all the pods with its labels
```
kubectl get po --show-labels
```

Now let's only list those pods which has label app=nginx
```
kubectl get po -l app=nginx
```

Now let's only list those pods which has label doesn't match app=nginx
```
kubectl get po -l app!=nginx
```

You could also label a pod/rs/deploy or any type of K8s objects
```
kubectl label pod/<pod-name> ver=1.18
kubectl label rs/<replicaset-name> ver=1.0
kubectl label deploy/<deploy-name> ver=1.0
```

This is how deployment selects its respective replicasets
```
kubectl get rs -l app=nginx
```

This is how replicaset selects its respective pods
```
kubectl get po -l app=nginx
```

### In case you haven't cloned TekTutor repository yet 
```
cd /home/rps
git clone https://github.com/tektutor/openshift-dec-2021.git
cd /home/rps/openshift-dec-2021
```

### K8s deployment using ConfigMaps
```
cd /home/rps/openshift-dec-2021
git pull
cd Day4/configmaps
kubectl apply -f configmap.yml
kubectl apply -f python-rest-dep.yml
```

Checking the configmap and deployments
```
kubectl get configmaps
kubectl get configmap
kubectl get cm
```

List the deploy to check the hello-ms deployment
```
kubectl get deploy
```

List the svc
```
kubectl get svc
kubectl describe svc/hello-ms
```

You may now access the hello-ms nodeport service 
```
curl http://master:31500/hello
curl http://node1:31500/hello
curl http://node2:31500/hello
```

### Rolling update
Assuming you already have an nginx deploy of image=nginx:1.8 running. You need to edit the image version from 1.18 to 1.19, save and exit.
```
kubectl edit deploy/nginx
```

You can now verify the deploy, rs and pod
```
kubectl get deploy,rs,po
```

You can check the status of the rolling update 
```
kubectl rollout status deploy/nginx
```

Now you can check the image version in the nginx deploy. You don't have to modify anything, you may quit vim.
```
kubectl edit deploy/nginx
```

If required, you may even undo the rolling update
```
kubectl rollout undo deploy/nginx
```

K8s will create one replicaset per version of image under the same deployment.

### Deploying MetalLB LoadBalancer in our on-prem K8s cluster
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml
```

You need to edit the configmap.yml in the openshift-dec-2021/Day4/metallb folder 
and the update IP Address from 
```
172.16.95.240-172.16.95.250 to 192.168.10.10-192.168.10.15
```
The 192.168.10.x 192.168.10.y range each one is choosing shouldn't conflict. Each one of you could assign 5 unique IP range for the Metallb Load Balancer.

Once you have updated the IP range you wish to assign for the Metallb LoadBalancer services you may create
```
kubectl apply -f configmap.yml
```

Check if the metallb is running
```
kubectl get all -n metallb-system
```

You may now create some LoadBalancer service for an existing deployment
```
kubectl delete svc/nginx 
kubectl expose deploy/nginx --type=LoadBalancer --port=80
```

You can check the load balancer is assigned with an external IP
```
kubectl get svc
```
The expected output is
<pre>
[root@master ingress]# kubectl get svc
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>          443/TCP        24h
<b>nginx        LoadBalancer   10.111.184.255   172.16.95.240   80:30621/TCP   9m42s</b>
</pre>
In the above nginx LoadBalancer service, observe the external IP, it would be in the range of IP that you configured in the configmap.yml.

You may now access the loadbalancer service as shown below
```
curl 172.16.95.240:80
```
### What is Helm?
- is a package manager for Kubernetes and OpenShift
- it let's package your application deployments, services and related manifest files in standard format
- helm helps you install, uninstall, upgrade and manage your application deployments in K8s and OpenShift
- the packaged application is called as Chart(s)
- there are 2 major verions 2.x and 3.x
- In 2.x Helm will install a component called Tiller which resides inside K8s cluster
- In 3.x Helm can deploy K8s applications without Tiller, we will using Helm 3

### Installing Helm Kubernetes Package Manager in your Lab machine
```
cd /home/rps/openshift-dec-2021
git pull
cd Day4/ingress
./get_helm.sh
```
### Setting up HELM Ingress repository
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm repo list
```

### What is an Ingress in Kubernetes ?
- Ingress is a routing rule that you define for different application services you exposed.
- Ingress can route calls to any time of K8s services ( NodePort, ClusterIP, LoadBalancer, etc., )
- You need an Ingress Controller that monitors new Ingress, or change in ingress and update those configurations
  on the fly in some Load Balancer(in our case Metallb).
- Ingress Controller comes out of the box in any cloud environment (AWS, Azure, GCP, etc.,)
- In case of on-prem bare-metal environments, we need to explicitly create Ingress Controllers and LoadBalancer
  before be define Ingress rules.

### Installing Nginx Controller

```
cd /home/rps/openshift-dec-2021
git pull
cd Day4/ingress

helm upgrade --install myingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace --values values.yml
```
Verify if ingress controller is ready
```
kubectl get all -n ingress-nginx
```

### Creating Ingress Rules
```
cd /home/rps/openshift-dec-2021
git pull
cd Day4/ingress

kubectl apply -f ingress.yml
```

### Listing the ingress resource
```
kubectl get ingress -w
```
The expected output is
<pre>
[root@master ingress]# kubectl get ingress
NAME               CLASS    HOSTS                   ADDRESS         PORTS   AGE
tektutor-ingress   <none>   tektutor.training.org   172.16.95.241   80      8h
</pre>

You may find more details about the ingress service as shown below
```
kubectl describe ingress/tektutor-ingress
```
The expected output is
<pre>
[root@master ingress]# kubectl describe ingress/tektutor-ingress 
Name:             tektutor-ingress
Labels:           <none>
Namespace:        default
Address:          172.16.95.241
Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
Rules:
  Host                   Path  Backends
  ----                   ----  --------
  tektutor.training.org  
                         /nginx   nginx:80 (192.168.104.51:80,192.168.104.52:80,192.168.166.170:80)
Annotations:             kubernetes.io/ingress.class: nginx
                         nginx.ingress.kubernetes.io/rewrite-target: /
Events:                  <none>
</pre>

Initialling the Address will be empty and in some time, K8s will assign an IP in the range in configured the metallb load balancer.

You need to append an entry in the /etc/hosts file
```
[root@master ingress]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
172.16.95.129	master
172.16.95.130	node1
172.16.95.131	node2
172.16.95.241   tektutor.training.org
```
You need to replace 172.16.95.241 with the IP address that shows up in your ingress.

Once /etc/hosts file is updated, you may now access your ingress as shown below
```
curl tektutor.training.org/nginx
```
The expected output is
<pre>
[root@master ingress]# curl tektutor.training.org/nginx
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
</pre>


