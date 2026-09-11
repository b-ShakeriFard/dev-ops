# Kubernetes Challenge: Deploy Nginx with NodePort

## Overview

This challenge deploys an **Nginx web server** using a Kubernetes `Deployment` with **3 replicas** and exposes it using a **NodePort Service**.

It demonstrates:

- Deployments and replicas
- Labels and selectors
- NodePort Services
- `port`, `targetPort`, and `nodePort`
- Service endpoint verification

## Architecture

```text
             Client
                |
        <NodeIP>:30011
                |
                v
        nginx-service
        NodePort Service
         30011 -> 80
                |
        selector:
        app=nginx-deployment
          /     |     \
         v      v      v
      Nginx   Nginx   Nginx
      Pod #1  Pod #2  Pod #3
       :80     :80     :80
```

## Requirements

1. Create a Deployment named `nginx-deployment`.
2. Use image `nginx:latest`.
3. Name the container `nginx-container`.
4. Run 3 replicas.
5. Create a NodePort Service named `nginx-service`.
6. Use NodePort `30011`.

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx-deployment

  template:
    metadata:
      labels:
        app: nginx-deployment

    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

Apply it:

```bash
kubectl apply -f deployment.yaml
```

## Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  type: NodePort

  selector:
    app: nginx-deployment

  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30011
```

Apply it:

```bash
kubectl apply -f svc.yaml
```

## Understanding the Ports

The Service contains:

```yaml
port: 80
targetPort: 80
nodePort: 30011
```

Their roles are:

```text
<NodeIP>:30011
       |
       v
NodePort: 30011
       |
Service port: 80
       |
       v
targetPort: 80
       |
       v
Nginx container
```

- **`nodePort`** — port exposed on Kubernetes nodes.
- **`port`** — port exposed by the Service.
- **`targetPort`** — destination port on the selected Pods.

Since Nginx listens on TCP port 80, the `targetPort` must be `80`, not `30011`.

## Labels and Selectors

The Deployment creates Pods with:

```yaml
labels:
  app: nginx-deployment
```

Therefore, the Service must select:

```yaml
selector:
  app: nginx-deployment
```

If these values do not match, the Service may exist but have **no backend Pods**.

## Verification

Check the Deployment and its Pods:

```bash
kubectl get deployment nginx-deployment
kubectl get pods -l app=nginx-deployment
```

Check the Service:

```bash
kubectl get svc nginx-service
```

Expected port mapping:

```text
80:30011/TCP
```

Check whether the Service discovered the Pods:

```bash
kubectl get endpoints nginx-service
```

In the successful lab, all three replicas appeared as endpoints on port 80.

On newer Kubernetes versions, the legacy `Endpoints` API is deprecated in favor of `EndpointSlice`. You can also inspect:

```bash
kubectl get endpointslices
```

Inspect the Service in detail:

```bash
kubectl describe svc nginx-service
```

If the node is reachable, test Nginx with:

```bash
curl http://<NODE-IP>:30011
```

## Troubleshooting

### Service has no endpoints

Compare the Service selector with the Pod labels:

```bash
kubectl get pods --show-labels
kubectl describe svc nginx-service
```

They must match.

### Wrong `targetPort`

Incorrect:

```yaml
targetPort: 30011
```

Correct:

```yaml
targetPort: 80
nodePort: 30011
```

`30011` is the externally exposed NodePort; Nginx itself is listening on port 80.

### Is `containerPort: 80` required?

No. A Service can route to `targetPort: 80` even if `containerPort: 80` is not explicitly declared.

However, declaring it is useful documentation and makes the Pod specification clearer.

## Key Takeaways

- Deployments manage replicated application Pods.
- Multiple replicas improve availability and scalability.
- Services provide stable access to dynamic Pods.
- NodePort exposes a Service through Kubernetes nodes.
- `nodePort`, `port`, and `targetPort` have different roles.
- Service selectors must match Pod labels.
- EndpointSlices or Endpoints can verify whether a Service has discovered its backend Pods.
- A successfully created Service does not automatically mean traffic routing is correctly configured.

## Interview Questions

**What does a Deployment do?**  
A Deployment declaratively manages replicated Pods and provides scaling, self-healing, rolling updates, and rollbacks.

**What is a NodePort Service?**  
A Service that exposes an application through a designated port on Kubernetes nodes.

**What is the difference between `port` and `targetPort`?**  
`port` is the Service-facing port, while `targetPort` is the destination port on the selected Pods.

**What is `nodePort`?**  
It is the port exposed on Kubernetes nodes for external access to a NodePort Service.

**How does a Service find its Pods?**  
Its selector matches labels assigned to the Pods.

**How can you check whether a Service has backend Pods?**  
Inspect its EndpointSlices or use `kubectl get endpoints <service-name>` where the legacy Endpoints API remains available.
