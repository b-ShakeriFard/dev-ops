# Harbor

Harbor is an open-source container registry designed for securely storing, managing, and distributing container images and OCI artifacts.

## Why Harbor?

Harbor adds enterprise features on top of a standard container registry, including:

- Private projects and repositories
- Role-Based Access Control (RBAC)
- Vulnerability scanning
- Robot accounts for CI/CD
- Image replication
- Image signing and trust features
- LDAP/OIDC authentication
- Garbage collection and retention policies

## Typical Workflow

```text
Developer
   ↓
Build Image
   ↓
docker push
   ↓
Harbor Registry
   ↓
Kubernetes / OpenShift
```

## Topics

This folder covers:

- Harbor installation
- Projects and repositories
- Users and robot accounts
- TLS certificates
- Vulnerability scanning
- Image replication
- Kubernetes integration
- Registry troubleshooting