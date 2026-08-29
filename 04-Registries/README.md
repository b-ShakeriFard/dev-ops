# Container Registries

Container registries store, manage, and distribute container images used by
Docker, Kubernetes, and CI/CD pipelines.

## Typical Flow

Developer
   ↓
Git Repository
   ↓
CI Pipeline
   ↓
Build Container Image
   ↓
Container Registry
   ↓
Kubernetes / Deployment Platform

## Topics

- Registry fundamentals
- Docker Registry
- Harbor
- Nexus Repository
- Authentication and TLS
- Kubernetes integration
- Image push/pull troubleshooting

## Common Registries

| Registry | Typical Use |
|---|---|
| Docker Hub | Public container images |
| Docker Registry | Lightweight private registry |
| Harbor | Enterprise/private container registry |
| Nexus Repository | Multi-format artifact and image repository |
| GitHub Container Registry | Registry integrated with GitHub |

## Core Commands

Login:

```bash
docker login registry.example.com
```

Tag an Image:
```bash
docker tag myapp:v1 registry.example.com/myproject/myapp:v1
```

Push:
```bash
docker push registry.example.com/myproject/myapp:v1
```

Pull:
```bash
docker pull registry.example.com/myproject/myapp:v1
```

