# Kubernetes Cheat Sheet

---

## Cluster Management

### Minikube Commands

```sh
# Start a local Kubernetes cluster with specific driver (docker, virtualbox, etc)
minikube start --driver=docker

# Open the Kubernetes dashboard (opens in default browser)
minikube dashboard

# Create network routes for LoadBalancer services (run in separate terminal)
minikube tunnel

# Pause the local cluster without destroying resources
minikube pause

# Stop the local cluster (preserves cluster state)
minikube stop

# Delete the local cluster and all resources
minikube delete

# List available images in Minikube's container runtime
minikube image ls

# SSH into minikube VM (for debugging)
minikube ssh
```

### Minikube Tips

```sh
# Use local Docker images by pointing to minikube's Docker daemon
eval $(minikube docker-env)

# Set default memory and CPU for minikube
minikube config set memory 8192
minikube config set cpus 4

# Enable ingress controller addon
minikube addons enable ingress
```

### AWS Vault Integration (for EKS access)

```sh
# Execute commands with AWS credentials (timeout after 1 hour)
aws-vault exec <profile-name> --duration=1h -- <command>

# Example: Run kubectl commands assuming role
aws-vault exec test-app -- kubectl get pods -n todo --context=prod-cluster

# List EKS clusters
aws-vault exec test-app -- aws eks list-clusters

# Update kubeconfig for EKS cluster
aws-vault exec test-app -- aws eks update-kubeconfig --name <cluster-name>
```

---

## Context and Namespace Management

```sh
# View merged kubeconfig settings
kubectl config view

# View current context
kubectl config current-context

# List all configured contexts
kubectl config get-contexts

# Switch to a different context
kubectl config use-context <context-name>

# Set default namespace for current context (persists in kubeconfig)
kubectl config set-context --current --namespace=<namespace-name>

# List all namespaces with labels
kubectl get namespaces --show-labels

# Create a new namespace with labels
kubectl create namespace <namespace-name> --labels=env=dev,team=backend

# Describe namespace details
kubectl describe namespace <namespace-name>

# Get resource quotas for a namespace
kubectl get quota -n <namespace-name>
```

---

## Basic Resource Management

### Pods

```sh
# List pods in current namespace (wide output shows IP and node)
kubectl get pods -o wide

# List pods with real-time updates (watch)
kubectl get pods -w

# List pods with wide output and watch combined
kubectl get pods -o wide -w

# Delete a pod
kubectl delete pod <pod-name>

# Delete all pods in a namespace (e.g., to restart)
kubectl delete pods --all -n <namespace>

# Delete completed pods (Succeeded status)
kubectl get pods --field-selector=status.phase==Succeeded -n <namespace> -o name | xargs kubectl delete -n <namespace>

# Delete failed pods
kubectl get pods --field-selector=status.phase==Failed -n <namespace> -o name | xargs kubectl delete -n <namespace>
```

### Deployments

```sh
# List deployments with labels
kubectl get deployments --show-labels

# List deployments in a namespace with labels
kubectl get deployments -n <namespace> --show-labels

# Get deployments cluster-wide with wide output
kubectl get deployments --all-namespaces -o wide

# Scale a deployment
kubectl scale deployment/<deployment-name> --replicas=3 -n <namespace>

# Rollout status of a deployment
kubectl rollout status deployment/<deployment-name> -n <namespace>

# Rollback to previous deployment revision
kubectl rollout undo deployment/<deployment-name> -n <namespace>

# Delete a deployment
kubectl delete deployment <deployment-name> -n <namespace>
```

### Services

```sh
# List services excluding ClusterIP type
kubectl get svc --field-selector spec.type!=ClusterIP

# List services sorted by name in a namespace
kubectl get service -n <namespace> --sort-by=.metadata.name

# Describe a service with events
kubectl describe svc <service-name> --show-events

# Delete a service immediately (no grace period)
kubectl delete service <service-name> --grace-period=0 --force -n <namespace>

# Delete a service normally
kubectl delete service <service-name> -n <namespace>

# Expose a deployment as a service
kubectl expose deployment/<deployment-name> --port=80 --target-port=8080 -n <namespace>
```

### Ingress

```sh
# List ingresses in all namespaces sorted by namespace
kubectl get ingress --all-namespaces --sort-by=.metadata.namespace

# List ingresses in a namespace with custom columns
kubectl get ingress -n <namespace> -o custom-columns=NAME:.metadata.name,CLASS:.spec.ingressClassName

# Describe ingress with verbose output
kubectl describe ingress -n <namespace> -v=6
```

---

## Pod Interaction and Debugging

```sh
# View pod logs with follow (stream)
kubectl logs -f <pod-name>

# Execute a command inside a pod (e.g., open bash shell)
kubectl exec -it <pod-name> -- /bin/bash

# Attach to a running container in a pod
kubectl attach <pod-name> -c <container-name>

# Port forward pod port to localhost
kubectl port-forward pod/<pod-name> 8080:80

# Run a temporary debug pod (busybox)
kubectl run -it --rm debug --image=busybox --restart=Never -- sh

# Run DNS resolution test pod
kubectl run -it --rm dns-test --image=busybox --restart=Never -- nslookup kubernetes.default

# Run network connectivity test pod
kubectl run -it --rm net-test --image=nicolaka/netshoot --restart=Never
```

---

## ConfigMaps and Secrets

```sh
# Create a ConfigMap from file or literal
kubectl create configmap <name> --from-file=<file>
kubectl create configmap <name> --from-literal=key=value

# View ConfigMaps
kubectl get configmap

# Describe a ConfigMap
kubectl describe configmap <name>

# Create a Secret from literals
kubectl create secret generic <name> --from-literal=username=admin --from-literal=password=secret

# View Secrets
kubectl get secret

# Decode secret value
kubectl get secret <name> -o jsonpath="{.data.password}" | base64 --decode
```

---

## Advanced Resource Management

```sh
# Edit a deployment with nano editor
KUBE_EDITOR="nano" kubectl edit deployment <deployment-name>

# Get detailed info on a pod
kubectl describe pod <pod-name>

# Get events sorted by timestamp (cluster or namespace)
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get events -n <namespace> --sort-by=.lastTimestamp

# Get Horizontal Pod Autoscaler details
kubectl get hpa -n <namespace> -o yaml

# Get resource usage metrics (requires metrics-server)
kubectl top pod
kubectl top node
```

---

## Rolling Updates and Rollbacks

```sh
# Perform a rolling update by setting new image
kubectl set image deployment/<deployment-name> <container-name>=<new-image>

# Check rollout status
kubectl rollout status deployment/<deployment-name>

# Undo rollout (rollback)
kubectl rollout undo deployment/<deployment-name>
```

---

## Resource Creation and Dry Run

```sh
# Preview resource changes without applying
kubectl apply -f <file.yaml> --dry-run=client

# Create deployment YAML template (dry-run)
kubectl create deployment <name> --image=nginx --dry-run=client -o yaml > deployment.yaml

# Apply resource manifest (create or update)
kubectl apply -f <file.yaml> -n <namespace>
```

---

## Bulk Cleanup

```sh
# Delete all pods in a namespace
kubectl delete pods --all -n <namespace>

# Delete pods with Succeeded or Failed phase
kubectl get pods --field-selector=status.phase==Succeeded -o name | xargs kubectl delete
kubectl get pods --field-selector=status.phase==Failed -o name | xargs kubectl delete
```

---

## Useful Aliases and Tips

```sh
# Aliases for frequent commands
alias k=kubectl
alias kgp="kubectl get pods"
alias kgs="kubectl get services"

# Use kubectl explain for docs on resources
kubectl explain deployment.spec.template.spec.containers

# Output resources in different formats
kubectl get pods -o json
kubectl get services -o yaml
kubectl get deployments -o wide
```

---

## Tools

### K9s (Kubernetes TUI)

```sh
# Install on MacOS (Homebrew)
brew install derailed/k9s/k9s

# Install on Linux (binary)
curl -sS https://webinstall.dev/k9s | bash
```

**Common K9s commands:**

- Switch namespaces: `:ns`
- View all resources: `:all`
- Filter by label: `:/app=frontend`
- View pod logs split: `<pod-name> + l`
- Delete resource with confirmation: `<resource> + ctrl-d`
- Press `0` to view all namespaces

* More info: [https://k9scli.io/topics/commands/](https://k9scli.io/topics/commands/)
* Switching contexts: https://stackoverflow.com/questions/73103387/how-do-i-switch-between-contexts-in-k9s-when-the-contexts-have-already-been-set

---

## Additional Resources

- [Official Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [Minikube GitHub](https://github.com/kubernetes/minikube)
- [K9s Documentation](https://k9scli.io/)

---

## Final Tips

- Use `alias k=kubectl` for faster CLI access.
- Use Helm for advanced app deployment and management.
- Use Lens or Octant GUI tools for cluster visualization.
- Always version control your YAML manifests with Git.
- Use dry-run mode to preview changes before applying.

---
