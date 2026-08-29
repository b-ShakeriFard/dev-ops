# Kubernetes Security

Kubernetes security covers how access to the cluster and workloads is controlled.

This section focuses on the main mechanisms used to secure users, Pods, and cluster resources:

- Authentication
- Authorization
- Role-Based Access Control (RBAC)
- ServiceAccounts
- Security Contexts
- Pod Security Standards
- Secrets
- Network Policies
- Admission Control

A simple Pod security example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
      securityContext:
        runAsNonRoot: true
        allowPrivilegeEscalation: false