# Kubernetes Challenge: Deploy Tomcat with NodePort

## Overview

In this challenge, we deployed a **Tomcat application on Kubernetes** using a dedicated namespace, a Deployment, and a NodePort Service.

The final traffic path was:

```text
Client
  |
  v
<NodeIP>:32227
  |
  v
tomcat-service-xfusion
  |
  v
Tomcat Pod :8080
```

## Requirements

- Namespace: `tomcat-namespace-xfusion`
- Deployment: `tomcat-deployment-xfusion`
- Replicas: `1`
- Container: `tomcat-container-xfusion`
- Image: `kodekloud/centos-ssh-enabled:tomcat`
- Container port: `8080`
- Service: `tomcat-service-xfusion`
- Service type: `NodePort`
- NodePort: `32227`

## 1. Create the Namespace

```bash
kubectl create namespace tomcat-namespace-xfusion
```

Verify:

```bash
kubectl get ns
```

## 2. Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment-xfusion
  namespace: tomcat-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat-deploy-xfusion
  template:
    metadata:
      labels:
        app: tomcat-deploy-xfusion
    spec:
      containers:
        - name: tomcat-container-xfusion
          image: kodekloud/centos-ssh-enabled:tomcat
          ports:
            - containerPort: 8080
```

Apply it:

```bash
kubectl apply -f deploy.yaml
```

Verify:

```bash
kubectl get pods -n tomcat-namespace-xfusion
```

The Pod should become:

```text
READY   STATUS
1/1     Running
```

## 3. NodePort Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service-xfusion
  namespace: tomcat-namespace-xfusion
spec:
  type: NodePort
  selector:
    app: tomcat-deploy-xfusion
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
      nodePort: 32227
```

Apply it:

```bash
kubectl apply -f svc.yaml
```

## Important Lesson: Service Selectors

The Service selector must match the labels on the Pods.

Deployment Pod label:

```yaml
labels:
  app: tomcat-deploy-xfusion
```

Therefore the Service needs:

```yaml
selector:
  app: tomcat-deploy-xfusion
```

Using something like:

```yaml
selector:
  app: tomcat-service-xfusion
```

would be wrong unless the Pods actually had that label.

A Service can exist perfectly well while routing traffic to **nothing** if its selector does not match any Pods.

## 4. Verify the Service

```bash
kubectl get svc -n tomcat-namespace-xfusion
```

We want the port mapping to show:

```text
8080:32227/TCP
```

This means:

- `8080` = Service port
- `8080` = target port on the Tomcat container
- `32227` = NodePort exposed on the Kubernetes node

## 5. Verify the Endpoint

A particularly useful troubleshooting command is:

```bash
kubectl get endpoints -n tomcat-namespace-xfusion
```

After the Pod became Running, the Service successfully showed a backend similar to:

```text
tomcat-service-xfusion   10.22.0.9:8080
```

This is excellent evidence that the Service selector found the Pod.

Initially, while the container was still being created, the endpoint appeared as:

```text
<none>
```

After the Pod became ready, Kubernetes populated the endpoint.

> Newer Kubernetes releases deprecate the legacy Endpoints API. You can also inspect EndpointSlices:

```bash
kubectl get endpointslices -n tomcat-namespace-xfusion
```

## Troubleshooting Flow

When a NodePort application is not reachable, check the chain in order:

```bash
kubectl get pods -n tomcat-namespace-xfusion
kubectl get svc -n tomcat-namespace-xfusion
kubectl get pods -n tomcat-namespace-xfusion --show-labels
kubectl get endpoints -n tomcat-namespace-xfusion
```

Think of it as:

```text
Is the Pod Running?
        |
        v
Does the Service exist?
        |
        v
Does selector match Pod label?
        |
        v
Does the Service have an endpoint?
        |
        v
Is the NodePort correct?
```

## Useful Commands

```bash
# Namespace
kubectl create namespace tomcat-namespace-xfusion

# Apply resources
kubectl apply -f deploy.yaml
kubectl apply -f svc.yaml

# Pods
kubectl get pods -n tomcat-namespace-xfusion

# Services
kubectl get svc -n tomcat-namespace-xfusion

# Labels
kubectl get pods -n tomcat-namespace-xfusion --show-labels

# Endpoints
kubectl get endpoints -n tomcat-namespace-xfusion

# Modern endpoint representation
kubectl get endpointslices -n tomcat-namespace-xfusion

# Detailed inspection
kubectl describe deployment tomcat-deployment-xfusion -n tomcat-namespace-xfusion
kubectl describe svc tomcat-service-xfusion -n tomcat-namespace-xfusion
```

## Key Takeaways

- Namespaces logically isolate Kubernetes resources.
- The Tomcat container listens on port `8080`.
- A NodePort Service makes the application reachable through a node port.
- `port` is the Service port.
- `targetPort` is the destination port on the Pod.
- `nodePort` is the externally exposed node port.
- Service selectors must match Pod labels.
- A Service with `<none>` endpoints is a strong clue that either the Pod is not ready or the selector does not match.
- Checking endpoints is one of the fastest ways to troubleshoot Service-to-Pod connectivity.

## Interview Questions

**Why does `containerPort: 8080` not expose Tomcat outside the cluster?**  
`containerPort` documents the port used by the container. External access requires a suitable Service, such as NodePort or LoadBalancer.

**What does `targetPort: 8080` mean?**  
Traffic received by the Service is forwarded to port `8080` on selected Pods.

**What does `nodePort: 32227` mean?**  
Kubernetes exposes the Service on port `32227` on the cluster nodes.

**How does the Service know which Tomcat Pod to use?**  
Its selector matches the labels attached to the Pod.

**Why might `kubectl get endpoints` show `<none>`?**  
The selector may not match any Pods, or matching Pods may not yet be ready.

## Result

The Tomcat application was successfully deployed with one replica, exposed through `tomcat-service-xfusion`, and connected to the Service through the Pod endpoint on port `8080`.

**Challenge completed successfully. 😎✌️**
