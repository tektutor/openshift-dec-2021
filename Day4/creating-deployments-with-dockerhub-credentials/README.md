kubectl create secret docker-registry myregistrykey --docker-server= https://index.docker.io/v1 \
        --docker-username=<your-dockerhub-username> --docker-password=<your-dockerhub-password> \
        --docker-email=<your-dockerhub-email>

kubectl apply -f pod.yml
