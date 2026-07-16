# 🚪 Linux Network Ports

> Understanding how Linux identifies and communicates with services.


---

### 🐧 Useful Commands Summary

| Command | Purpose |
|-|-|
| `ss -tuln` | Show listening ports |
| `ss -tulpn` | Show ports and processes |
| `nc -zv` | Test remote ports |
| `nmap` | Scan open ports |
| `curl` | Test HTTP services |
| `firewall-cmd` | Manage firewall ports |


---

# 🔎 What is a Port?

An IP address identifies a **machine**.

A port identifies a **service running on that machine**.

Example:

```text
192.168.1.50:22
```

Means:

```text
192.168.1.50

    |
    v

Linux Server


22

    |
    v

SSH Service
```

A connection requires:

```
Source IP + Source Port

        +

Destination IP + Destination Port
```

---

# 🔢 Port Range

TCP and UDP ports use numbers from:

```text
0 - 65535
```

They are divided into categories:

| Range | Name | Purpose |
|-|-|-|
| 0 - 1023 | Well-known ports | Common system services |
| 1024 - 49151 | Registered ports | Applications and services |
| 49152 - 65535 | Dynamic/Ephemeral ports | Temporary client connections |

---

# 🚚 TCP vs UDP Ports

Ports exist in both TCP and UDP.

Example:

```text
TCP 443
```

and:

```text
UDP 443
```

are different communication channels.

---

## TCP Ports

TCP provides:

- Reliable delivery
- Connection establishment
- Ordered communication

Common examples:

```text
SSH      → TCP 22
HTTPS    → TCP 443
LDAP     → TCP 389
```

---

## UDP Ports

UDP provides:

- Faster communication
- No connection guarantee

Common examples:

```text
DNS      → UDP 53
DHCP     → UDP 67/68
```

---

# 🌐 Common Linux Service Ports

| Port | Protocol | Service |
|-|-|-|
| 22 | TCP | SSH |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 123 | UDP | NTP |
| 389 | TCP/UDP | LDAP |
| 443 | TCP | HTTPS |
| 636 | TCP | LDAPS |
| 6443 | TCP | Kubernetes API |
| 2379/2380 | TCP | etcd |
| 10250 | TCP | kubelet |

---

# 👀 View Listening Ports

The main Linux tool:

```bash
ss
```

---

## Show listening TCP/UDP ports

```bash
ss -tuln
```

Example:

```text
tcp   LISTEN 0.0.0.0:22
tcp   LISTEN 0.0.0.0:443
```

Meaning:

```text
Port 22  → SSH

Port 443 → HTTPS
```

---

# 🔍 Find Which Process Uses a Port

Use:

```bash
sudo ss -tulpn
```

Example:

```text
tcp LISTEN

0.0.0.0:6443

users:
kube-apiserver
```

Meaning:

```text
Kubernetes API Server

is listening on port 6443
```

---

# 🧪 Test a Remote Port

The `nc` (netcat) command can test connectivity.

Example:

```bash
nc -zv 192.168.1.20 22
```

Successful result:

```text
Connection succeeded
```

Meaning:

```text
SSH port is reachable
```

---

Test HTTPS:

```bash
nc -zv google.com 443
```

---

# 🌍 Check Open Ports with nmap

`nmap` scans network ports.

Example:

```bash
nmap 192.168.1.20
```

Output:

```text
PORT     STATE

22/tcp   open

443/tcp  open
```

Meaning:

```text
SSH available

HTTPS available
```

---

# 🔐 Ports and Firewalls

A service may be running but unreachable because a firewall blocks the port.

Example:

```
Application

     |

     v

Port 443

     |

     X

Firewall blocks traffic
```

Check firewall:

```bash
firewall-cmd --list-ports
```

Open a port:

```bash
sudo firewall-cmd \
--add-port=443/tcp \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

---

# 🌐 Ports and Applications

Example:

A web server:

```
Browser

   |

TCP 443

   |

nginx

   |

HTML Response
```

A database:

```
Application

   |

TCP 5432

   |

PostgreSQL
```

---

# ☸️ Ports in Kubernetes

Kubernetes uses ports at multiple levels.

Example:

```
User

 |

NodePort :30080

 |

Service :80

 |

Pod :8080

 |

Application
```

Example:

```yaml
ports:
- port: 80
  targetPort: 8080
```

Meaning:

```text
Service listens on:

80


Container listens on:

8080
```

---

# 🛠️ Port Troubleshooting Workflow

```text
1. Is the service running?

        |
        v

systemctl status service


2. Is the port listening?

        |
        v

ss -tulpn


3. Is the firewall allowing traffic?

        |
        v

firewall-cmd


4. Can I reach the port?

        |
        v

nc -zv host port
```



# Conclusion

Ports allow multiple services to run on the same Linux system while remaining uniquely identifiable.

Understanding ports helps administrators troubleshoot services, configure firewalls, and manage applications.

This concept becomes even more important in container and Kubernetes environments, where services, pods, and ingress controllers communicate through ports.