## Minikube

### https://kubernetes.io/docs/tutorials/hello-minikube/

```sh
minikube start
minikube dashboard
minikube tunnel
minikube stop
minikube delete
```

```sh
kubectl get deployments
kubectl get pods
kubectl get events
kubectl config view
kubectl logs hello-node-5f76cf6ccf-br9b5
kubectl get services
kubectl delete service hello-node
kubectl delete deployment hello-node
```

### Kubernetes create namespace

- https://kodekloud.com/blog/kubectl-create-namespace/
- https://medium.com/@xcoulon/tips-for-minikube-users-including-myself-creating-and-using-a-custom-namespace-37f5ebb7319f

```sh
kubectl get namespaces
kubectl create namespace nginx-app
```

### Deployment

```sh
kubectl apply -f nginx-deployment.yaml  -n nginx-app
kubectl get deployment -n nginx-app
kubectl delete deployment nginx-deployment -n nginx-app
kubectl get service -n nginx-app
kubectl delete service nginx-service -n nginx-app
```

### Some More

```sh
kubectl describe ingress -n default
kubectl describe hpa -n default
kubectl get svc
kubectl describe svc
kubectl get deployment
kubectl edit deployment
kubectl get events -n nginx-app
```

### minikube pull image from local docker deployment

https://www.geeksforgeeks.org/how-to-use-local-docker-images-with-minikube/

```sh
minikube image ls
```

## K9cli

### Installation

https://k9scli.io/topics/install/

### switch namespace in k9s

- https://k9scli.io/topics/commands/
- https://stackoverflow.com/questions/73103387/how-do-i-switch-between-contexts-in-k9s-when-the-contexts-have-already-been-set

`:namespaces`
