# ☸️ From Linux Networking to Kubernetes Networking

> How traditional TCP/IP concepts become Kubernetes networking.

---

# 🐧 Linux TCP Sessions

On Linux, applications communicate through TCP/UDP sockets.

Example:

```bash
ss -tulnp
```

Shows:

```
Application

      |

TCP Port

      |

Network Interface

      |

IP Address
```

Example:

```
nginx

LISTEN

0.0.0.0:443
```

Meaning:

The server is accepting HTTPS connections.

---

# ☸️ Kubernetes Services and TCP Ports

A Kubernetes Service provides stable network access to Pods.

Example:

```yaml
apiVersion: v1
kind: Service

spec:
  ports:
  - port: 80
    targetPort: 8080
```

Traffic flow:

```
Client

 |

Service IP :80

 |

Pod IP :8080

 |

Application
```

The Service creates a stable endpoint even when Pods change.

---

# 🌐 Ingress and HTTP Layer

Ingress operates at the application layer.

It understands:

- HTTP
- HTTPS
- Hostnames
- URLs


Example:

```
User

 |

HTTPS

 |

Ingress Controller

 |

Service

 |

Pod
```

Example:

```
app.example.com

        |

        v

nginx-service

        |

        v

frontend-pod
```

---

# 🔌 CNI and Network Layer

Containers need networking.

The Container Network Interface (CNI) provides:

- Pod IP addresses
- Network connectivity
- Routing
- Network isolation


Example:

```
Pod

 |

CNI Plugin

 |

Linux Network Namespace

 |

Host Network

 |

External Network
```

Examples:

- Calico
- Cilium
- Flannel
- OVN-Kubernetes

---

# 🔐 NetworkPolicy and Traffic Control

NetworkPolicy controls communication between workloads.

Example:

Without NetworkPolicy:

```
Pod A  ---> Pod B
Pod A  ---> Pod C
Pod A  ---> Database
```

With NetworkPolicy:

```
Pod A

   |

Allowed

   v

Database


Blocked

   X

Other Pods
```

NetworkPolicy provides:

- Micro-segmentation
- Zero-trust networking
- Application isolation

---

# 🔄 Complete Picture

```
Application
    |
    v
HTTP / HTTPS
    |
    v
Service
    |
    v
TCP Port
    |
    v
Pod IP
    |
    v
CNI Network
    |
    v
Linux Network Stack
    |
    v
Physical Network
```

---

# Conclusion

Kubernetes networking is not separate from Linux networking.

It extends traditional TCP/IP concepts by adding:

- Dynamic service discovery
- Container networking
- Software-defined networking
- Network security policies

Strong Linux networking knowledge is the foundation for understanding Kubernetes networking.
