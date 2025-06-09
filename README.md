# Kubernetes Cheat Sheet

## Minikube Commands

Basic Minikube commands for local Kubernetes development:

```sh
# Start a local Kubernetes cluster
minikube start

# Open the Kubernetes dashboard
minikube dashboard

# Create network routes for LoadBalancer services
minikube tunnel

# Stop the local cluster
minikube stop

# Delete the local cluster
minikube delete

# List available images in Minikube
minikube image ls
```

## Basic Kubectl Commands

```sh
# View cluster information
kubectl config view

# Get deployments in current namespace
kubectl get deployments

# Get pods in current namespace
kubectl get pods

# View cluster events
kubectl get events

# View pod logs
kubectl logs <pod-name>

# Get services in current namespace
kubectl get services

# Delete a service
kubectl delete service <service-name>

# Delete a deployment
kubectl delete deployment <deployment-name>
```

## Namespace Operations

- https://kodekloud.com/blog/kubectl-create-namespace/
- https://medium.com/@xcoulon/tips-for-minikube-users-including-myself-creating-and-using-a-custom-namespace-37f5ebb7319f

```sh
# List all namespaces
kubectl get namespaces

# Create a new namespace
kubectl create namespace <namespace-name>

# Set default namespace for current context
kubectl config set-context --current --namespace=<namespace-name>
```

## Deployment and Service Management

```sh
# Apply a deployment from YAML file
kubectl apply -f <file.yaml> -n <namespace>

# Get deployments in specific namespace
kubectl get deployment -n <namespace>

# Delete a deployment
kubectl delete deployment <deployment-name> -n <namespace>

# Get services in specific namespace
kubectl get service -n <namespace>

# Delete a service
kubectl delete service <service-name> -n <namespace>
```

## Inspection Commands

```sh
# Describe ingress in default namespace
kubectl describe ingress -n default

# Describe Horizontal Pod Autoscaler
kubectl describe hpa -n default

# Get all services
kubectl get svc

# Describe services
kubectl describe svc

# Get all deployments
kubectl get deployment

# Edit a deployment
kubectl edit deployment <deployment-name>

# Get events in specific namespace
kubectl get events -n <namespace>
```

## AWS Vault Integration

```sh
# Execute commands with AWS credentials
aws-vault exec <profile-name> -- <command>

# Example with kubectl commands
aws-vault exec test-app -- kubectl get pods -n todo
```

## Resource Listing Commands

### Namespace-specific listings
```sh
kubectl get pods -n todo
kubectl get ingress -n todo
kubectl get deployments -n todo
```

### Cluster-wide listings
```sh
kubectl get deployments --all-namespaces
kubectl get ingress --all-namespaces
kubectl get pods --all-namespaces
```

## K9s CLI (Kubernetes TUI)

### Installation
See official installation guide: https://k9scli.io/topics/install/

### switch namespace in k9s
- https://k9scli.io/topics/commands/
- https://stackoverflow.com/questions/73103387/how-do-i-switch-between-contexts-in-k9s-when-the-contexts-have-already-been-set

### Common Commands
```sh
# Switch between namespaces
:namespaces

# View commands help
:help
```

## Tips
- To use local Docker images with Minikube, build directly in Minikube's Docker daemon:
  ```sh
  eval $(minikube docker-env)
  docker build -t <image-name> .
  ```
- In K9s, press '0' to view all namespaces
- Use `kubectl explain <resource>` to get documentation for any Kubernetes resource
```

This revised version:
1. Organizes commands by category
2. Provides clear descriptions
3. Includes all requested commands
4. Adds helpful tips
5. Maintains consistent formatting
6. Includes both basic and advanced operations
