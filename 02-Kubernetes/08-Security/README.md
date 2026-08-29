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

```


For this folder, I’d probably structure it roughly like:

```text
08-Security/
├── README.md
├── Authentication.md
├── Authorization.md
├── RBAC.md
├── ServiceAccounts.md
├── Security-Contexts.md
├── Pod-Security-Standards.md
├── Network-Policies.md
└── Admission-Control.md
```