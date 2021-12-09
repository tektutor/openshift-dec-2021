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
