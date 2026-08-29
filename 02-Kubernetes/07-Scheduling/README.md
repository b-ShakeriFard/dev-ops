# Kubernetes Scheduling

Kubernetes scheduling determines **which Node runs each Pod**.

By default, the Kubernetes Scheduler evaluates available Nodes and selects a suitable one based on resource availability, constraints, and scheduling rules.

This section covers the main mechanisms used to influence Pod placement:

```text
- `nodeSelector`
- Node Affinity and Anti-Affinity
- Pod Affinity and Anti-Affinity
- Taints and Tolerations
- Resource Requests and Limits
- Manual Scheduling
- Priority Classes and Preemption
- Topology Spread Constraints
```

A simple example using `nodeSelector`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: scheduled-pod
spec:
  nodeSelector:
    disk: ssd

  containers:
    - name: nginx
      image: nginx:latest
```