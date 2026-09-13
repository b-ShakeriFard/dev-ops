# Kubernetes Challenge: Deploy Grafana with a NodePort Service

## Overview

In this challenge, we deployed **Grafana on Kubernetes** and exposed its web interface using a **NodePort Service**.

The exercise covered:

- Creating a Kubernetes Deployment
- Running Grafana on its default application port
- Creating a NodePort Service
- Matching Service selectors with Pod labels
- Verifying Service endpoints
- Confirming that the Grafana login page is reachable from the browser

## Architecture

```text
Browser
   |
   | <NodeIP>:32000
   v
grafana-service
NodePort Service
   |
   | targetPort: 3000
   v
Grafana Pod
   |
   v
grafana/grafana:latest
Port 3000
```

## Challenge Requirements

1. Create a Deployment named `grafana-deployment-devops`.
2. Use any suitable Grafana image.
3. Create a `NodePort` Service.
4. Expose the application using `nodePort: 32000`.
5. Verify that the Grafana login page is accessible.

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana-deployment-devops
  template:
    metadata:
      labels:
        app: grafana-deployment-devops
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:latest
          ports:
            - containerPort: 3000
              protocol: TCP
```

Apply it:

```bash
kubectl apply -f deploy.yaml
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
1/1   Running
```

## Grafana Port

Grafana listens on port `3000`.

```text
Browser
   |
<NodeIP>:32000
   |
NodePort Service
   |
targetPort: 3000
   |
Grafana container :3000
```

## Generate the Service YAML

A useful shortcut:

```bash
kubectl create svc nodeport grafana-service   --tcp=3000:3000   --dry-run=client -o yaml > svc.yaml
```

This generates a YAML skeleton without creating the Service immediately.

## Final Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana-deployment-devops
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 32000
```

Apply it:

```bash
kubectl apply -f svc.yaml
```

## Understanding the Ports

```yaml
port: 3000
targetPort: 3000
nodePort: 32000
```

- `port` = Service port inside the cluster.
- `targetPort` = destination port on the Grafana Pod.
- `nodePort` = externally reachable port on Kubernetes nodes.

## Labels and Selectors

The Deployment creates Pods with:

```yaml
labels:
  app: grafana-deployment-devops
```

Therefore the Service must use:

```yaml
selector:
  app: grafana-deployment-devops
```

If the Service selector does not match the Pod label, the Service will have no backend endpoint.

## Verification

Check Pods:

```bash
kubectl get pods
```

Check Service:

```bash
kubectl get svc
```

Expected port mapping:

```text
3000:32000/TCP
```

Check endpoints:

```bash
kubectl get endpoints
```

In the successful lab, Grafana appeared as:

```text
grafana-service   10.22.0.9:3000
```

This proved that the Service successfully discovered the Grafana Pod.

On newer Kubernetes versions, inspect EndpointSlices as well:

```bash
kubectl get endpointslices
```

## Browser Verification

The final test was opening Grafana through NodePort `32000`.

Conceptually:

```text
http://<NODE-IP>:32000
```

The Grafana login page loaded successfully, confirming that the full networking path worked.

## Troubleshooting

### Service has no endpoints

Check:

```bash
kubectl get pods --show-labels
kubectl describe svc grafana-service
```

The Service selector must match the Pod label.

### Pod is not running

Use:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### Grafana UI is not reachable

Verify:

```bash
kubectl get pods
kubectl get svc
kubectl get endpoints
```

Then confirm:

```text
NodePort = 32000
targetPort = 3000
Grafana Pod is listening on 3000
```

## Useful Cheat Sheet

```bash
kubectl create deployment grafana-deployment-devops   --image=grafana/grafana:latest   --dry-run=client -o yaml > deploy.yaml

kubectl create svc nodeport grafana-service   --tcp=3000:3000   --dry-run=client -o yaml > svc.yaml

kubectl apply -f deploy.yaml
kubectl apply -f svc.yaml

kubectl get pods
kubectl get svc
kubectl get pods --show-labels
kubectl get endpoints
kubectl get endpointslices
```

## Key Takeaways

- Grafana listens on port `3000` by default.
- A Deployment manages the Grafana Pod.
- A NodePort Service exposes Grafana outside the cluster.
- `nodePort: 32000` is the external node port.
- `targetPort: 3000` forwards traffic to Grafana.
- Service selectors must match Pod labels.
- A running Pod does not guarantee application reachability.
- Endpoints help confirm Service-to-Pod connectivity.
- Browser verification confirms the application is actually reachable.
- `kubectl --dry-run=client -o yaml` is an excellent way to generate reusable manifests.

## Interview Questions

**What port does Grafana use by default?**  
Grafana's web interface listens on port `3000`.

**What is a NodePort Service?**  
A Service type that exposes an application through a static port on Kubernetes nodes.

**What does `targetPort` represent?**  
The port on the selected Pod to which the Service forwards traffic.

**What does `nodePort` represent?**  
The externally accessible port on Kubernetes nodes.

**How does a Service discover Pods?**  
By matching selectors against Pod labels.

**What happens if a Service selector matches no Pods?**  
The Service exists but has no backend endpoints.

**How do you verify Service backends?**  
Use `kubectl get endpoints` or inspect EndpointSlice resources.

**What does `--dry-run=client -o yaml` do?**  
It generates the resource definition locally as YAML without creating it.

## Result

The Grafana Pod was successfully deployed, exposed through NodePort `32000`, discovered by the Service on port `3000`, and the Grafana login page loaded successfully.

**Challenge completed successfully. 😎✌️**
