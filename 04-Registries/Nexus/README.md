# Nexus Repository

Nexus Repository is an artifact repository manager used to store, manage, and distribute software packages, container images, and build artifacts.

## Why Nexus?

Nexus supports many repository formats, including:

- Docker / OCI images
- Maven
- npm
- PyPI
- NuGet
- Helm
- Raw files

It can also act as a proxy and cache for external repositories.

## Typical Workflow

```text
Developer
   ↓
Build Artifact / Image
   ↓
Push to Nexus
   ↓
Nexus Repository
   ↓
CI/CD / Kubernetes / Applications
```

## Topics

This folder covers:

- Nexus installation
- Repository types
- Docker repositories
- Authentication and access control
- TLS certificates
- Proxy repositories
- Cleanup policies
- Kubernetes integration
- Troubleshooting