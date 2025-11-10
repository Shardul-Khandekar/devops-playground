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

