### In case you don't have nginx deployment and service created already
```
kubectl create deploy nginx --image=nginx:1.18
kubectl scale deploy/nginx --replicas=6
kubectl expose deploy/nginx --type=NodePort --port=80
```

### Installing HELM 3
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

### Setting up HELM Ingress repository
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm repo list
```

### Installing Nginx Controller

```
helm upgrade --install myingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace --values values.yml
```

### Creating Ingress Rules
```
kubectl apply -f ingress.yml
```
