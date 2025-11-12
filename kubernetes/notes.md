# Kubernetes Notes

## Quick Minikube Commands

```powershell
minikube start --no-vtx-check
```
- Starts Minikube while skipping VT-x checks (useful on some Windows hosts).

```powershell
minikube start --driver=docker
```
- Starts Minikube using the Docker driver.

## Pods

Pod is the smallest deployable unit in Kubernetes. A pod wraps one or more containers and provides Kubernetes-specific metadata and configuration such as labels, restart policies, networking, and shared volumes.

**Key rules:**

- A pod usually runs a single main container; sidecars/helper containers are used when needed.
- Containers in the same pod share the same IP address and storage volumes.
- Pods are ephemeral: their IPs change and a plain pod will not be recreated automatically if deleted.

## Basic Pod Commands

```bash
kubectl apply -f nginx-pod.yaml
```
- Create or update resources declaratively from the `nginx-pod.yaml` manifest.

```bash
kubectl get pods
```
- List pods and show their status.

```bash
kubectl describe pod my-first-pod
```

- Show detailed information and recent events for the specified Pod (useful for debugging).

```bash
kubectl port-forward my-first-pod 8080:80
```

- Forward local port `8080` to port `80` on `my-first-pod` (exposes the Pod to the local host).

```bash
kubectl delete pod my-first-pod
```

- Delete the specified Pod from the cluster.

## Pod lifecycle notes

- Pods are transient: when deleted they are gone unless managed by a controller (ReplicaSet, Deployment, etc.).
- Pod IP addresses are ephemeral: a replaced Pod receives a new IP.

## ReplicaSets

ReplicaSets ensure a specified number of identical Pod replicas are running; they monitor and replace Pods to maintain the desired replica count.

Notes:

- A ReplicaSet can be created directly, but in practice Deployments are used to manage ReplicaSets.
- ReplicaSets track the number of pod replicas but do not manage rolling updates between different container versions.

Example:

- If you change a container image from `nginx:1.0` to `nginx:2.0`, a ReplicaSet will ensure the desired count of Pods but won't perform a versioned rollout on its own.

```bash
kubectl get replicasets
```

- List ReplicaSets in the current namespace and their replica counts.

## Deployments

Deployments manage ReplicaSets and add higher-level features such as declarative rollouts, rollbacks, scaling, and versioning.

Advantages of Deployments:

- Perform rolling updates between versions.
- Keep rollout history to allow rollbacks.
- Automatically create and manage underlying ReplicaSets.

The Deployment swaps or creates ReplicaSets when the Pod template changes (for example when the container image changes).

## Inspecting API resources

```powershell
kubectl api-resources | Select-String -Pattern "pod|deployment"
```

- Show cluster API resources and filter for entries containing `pod` or `deployment` (PowerShell-compatible).

## How Kubernetes tracks state

- Kubernetes uses Watches and the reconciliation loop (controllers) rather than traditional polling: controllers observe desired state and reconcile actual state to match it.

## Port-forwarding a Deployment

- When you port-forward a Deployment, `kubectl` selects one running Pod from the Deployment and forwards to it (one chosen Pod).

```bash
kubectl port-forward deployment/nginx-deployment 8080:80
```

- Forward local port `8080` to port `80` on one Pod selected from the `nginx-deployment`.

## Services

- A Service acts as a stable frontend for a set of Pods. Services route traffic to Pods that match their selector labels.
- When additional Pods matching the selector are created, the Service automatically includes them; you do not need to restart the Service.

```bash
kubectl get services
minikube service nginx-service
```

```
