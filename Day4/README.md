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

### In case you haven't clone TekTutor repository yet 
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
