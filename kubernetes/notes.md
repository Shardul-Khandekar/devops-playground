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
- Show detailed information and recent events for the specified pod (useful for debugging).

```bash
kubectl port-forward my-first-pod 8080:80
```
- Forward local port `8080` to port `80` on `my-first-pod` (exposes the pod to the local host).

```bash
kubectl delete pod my-first-pod
```
- Delete the specified pod from the cluster.

## Pod lifecycle notes

- Pods are mortal: when deleted they are gone unless managed by a controller (ReplicaSet, Deployment, etc.).
- Pod IP addresses are ephemeral: a new pod gets a new IP when recreated.

## ReplicaSets

ReplicaSets ensure a specified number of identical pod replicas are running; they monitor and replace pods to maintain the desired replica count.

Notes:

- A ReplicaSet can be created directly, but in practice Deployments are used to manage ReplicaSets.
- ReplicaSets track the number of pod replicas but do not manage rolling updates between different container versions.

Example conceptual behavior:

- If you change a container image from `nginx:1.0` to `nginx:2.0`, a ReplicaSet only ensures the desired count of pods — it does not handle versioned rollouts by itself.

```bash
kubectl get replicasets
```
- List ReplicaSets in the current namespace and their replica counts.

## Deployments

Deployments manage ReplicaSets and provide higher-level features such as declarative rollouts, rollbacks, scaling, and versioning.

Advantages of Deployments over direct ReplicaSet usage:

- Perform rolling updates between versions.
- Keep rollout history to allow rollbacks.
- Automatically create and manage underlying ReplicaSets.

The ReplicaSet maintains the desired pod count; the Deployment manages versioned rollouts by creating or swapping ReplicaSets when the pod template (for example the container image) changes.

## Inspecting API resources

```powershell
kubectl api-resources | Select-String -Pattern "pod|deployment"
```
- Show cluster API resources and filter for entries containing `pod` or `deployment` (PowerShell-compatible).

## How Kubernetes tracks state

- Kubernetes uses Watches and the Reconciliation Loop (controllers) rather than traditional polling: controllers observe desired state and reconcile actual state to match it.

## Port-forwarding a Deployment

- When you port-forward a Deployment, `kubectl` selects one running pod from the Deployment at random and forwards to it.

```bash
kubectl port-forward deployment/nginx-deployment 8080:80
```
- Forward local port `8080` to port `80` on one pod selected from the `nginx-deployment`.