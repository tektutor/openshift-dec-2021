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

### Deploying MetalLB LoadBalancer in K8s cluster
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
<b>nginx        LoadBalancer   10.111.184.255   172.16.95.240   80:30621/TCP   9m42s</nginx>
</pre>