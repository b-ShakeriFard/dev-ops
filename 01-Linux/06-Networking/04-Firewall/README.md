# 🔥 Linux Firewall

> Controlling network traffic and protecting Linux systems using firewalld.

---

# Overview

A firewall controls which network connections are:

- ✅ Allowed
- ❌ Blocked
- 🔒 Restricted

A service may be running correctly, but the firewall can still prevent access.

Example:

```
Client

   |

   v

Port 443

   |

   v

🔥 Firewall

   |

   v

Web Server
```

---

# 🎯 Why Do We Need Firewalls?

Without a firewall:

```
Any host

   |

   v

Any service

   |

   v

Linux Server
```

With a firewall:

```
Allowed Traffic

        |

        v

🔥 Firewall

        |

        v

Protected Services
```

Firewalls provide:

- Access control
- Network segmentation
- Attack surface reduction
- Service protection

---

# 🧱 Linux Firewall Stack

Modern Enterprise Linux uses:

```
Application

     |

     v

Network Socket

     |

     v

firewalld

     |

     v

nftables

     |

     v

Linux Kernel
```

---

# 🔥 firewalld

`firewalld` is the default firewall management service in:

- RHEL
- Rocky Linux
- AlmaLinux
- Fedora

Check status:

```bash
systemctl status firewalld
```

Check firewall state:

```bash
firewall-cmd --state
```

Example:

```
running
```

---

# 🛠️ Main Tool

The command-line tool:

```bash
firewall-cmd
```

is used to:

- View rules
- Add services
- Open ports
- Manage zones
- Reload configuration

Example:

```bash
firewall-cmd --list-all
```

---

# 🧩 Firewall Concepts

## Zones

Zones define trust levels.

Example:

```
public

    |
    v

Internet-facing servers
```

```
trusted

    |
    v

Internal networks
```

Common zones:

| Zone | Purpose |
|-|-|
| trusted | Allow almost everything |
| public | Default external network |
| internal | Internal networks |
| dmz | Public-facing services |
| block | Block traffic |

---

# 🚪 Ports

Services communicate through ports.

Examples:

| Service | Port |
|-|-|
| SSH | 22 |
| HTTP | 80 |
| HTTPS | 443 |
| LDAP | 389 |
| Kubernetes API | 6443 |

Firewall controls access to these ports.

Example:

```
Allow TCP 443

        |

        v

HTTPS traffic accepted
```

---

# 🔧 Services

Instead of opening ports manually, firewalld provides predefined services.

Example:

```bash
firewall-cmd --add-service=http
```

Equivalent to:

```text
Allow TCP port 80
```

---

# ⏱️ Runtime vs Permanent Rules

Firewalld has two configurations.

## Runtime

Temporary:

```
Lost after reboot
```

Example:

```bash
firewall-cmd --add-port=8080/tcp
```

---

## Permanent

Survives reboot:

```bash
firewall-cmd \
--add-port=8080/tcp \
--permanent
```

Apply:

```bash
firewall-cmd --reload
```

---

# 🖥️ Real Linux Example

Scenario:

A Rocky Linux web server runs nginx.

Current:

```
nginx

TCP 443

Blocked by firewall
```

Solution:

Check rules:

```bash
firewall-cmd --list-all
```

Allow HTTPS:

```bash
firewall-cmd \
--add-service=https \
--permanent
```

Reload:

```bash
firewall-cmd --reload
```

Test:

```bash
curl https://server.example.com
```

---

# ☸️ DevOps Connection

Firewalls are critical in:

## Kubernetes

Examples:

```
NodePort

30000-32767
```

## OpenShift

Examples:

```
API Server

6443
```

## Harbor

Examples:

```
HTTPS

443
```

## LDAP

Examples:

```
389 / 636
```

A blocked firewall rule can make healthy services appear broken.

---

# 🔍 Troubleshooting Flow

When a service is unreachable:

```
Application

      |

      v

Is service running?

ss -tulpn


      |

      v

Is firewall allowing traffic?

firewall-cmd


      |

      v

Can client connect?

curl / nc

```

---

# 📂 Folder Structure

```
04-Firewall/

├── README.md

├── firewalld.md

├── zones.md

├── services.md

├── ports.md

└── troubleshooting.md
```

---

# Conclusion

A firewall is not just a security component; it is part of normal Linux administration.

Understanding `firewalld`, zones, services, and ports allows administrators to securely expose applications while protecting systems.

Firewall knowledge is essential for managing Linux servers, containers, Kubernetes clusters, and enterprise infrastructure.