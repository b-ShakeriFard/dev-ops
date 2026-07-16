# 🌐 TCP/IP Model

> The foundation of modern Linux and Internet communication.

---

## Overview

The TCP/IP model describes how data moves between systems over a network.

Unlike the theoretical OSI model, TCP/IP is the practical networking model used by:

- Linux systems
- The Internet
- Cloud platforms
- Containers
- Kubernetes

The model explains how applications communicate, how data is transported, and how systems find each other across networks.

---

# 🏗️ TCP/IP Layers

The TCP/IP model consists of four layers:

```text
Application Layer

Transport Layer

Internet Layer

Network Access Layer
```

Each layer has a specific responsibility.

---

# 🖥️ 1. Application Layer

The Application layer provides network services directly to applications.

Examples:

| Protocol | Purpose |
|-|-|
| HTTP | Web communication |
| HTTPS | Secure web communication |
| SSH | Remote administration |
| DNS | Name resolution |
| LDAP | Directory services |
| SMTP | Email communication |

Example:

```text
Browser
   |
   v
HTTPS
   |
   v
Web Server
```

Linux administrators interact with this layer every day.

---

# 🚚 2. Transport Layer

The Transport layer provides communication between applications.

The two main protocols are:

## TCP

Transmission Control Protocol

Characteristics:

✅ Reliable  
✅ Connection-oriented  
✅ Ordered delivery  
✅ Error checking  

Used by:

- SSH
- HTTPS
- Databases
- File transfers


## UDP

User Datagram Protocol

Characteristics:

✅ Fast  
✅ Connectionless  
❌ No delivery guarantee  

Used by:

- DNS queries
- Streaming
- Real-time applications

---

# 🚪 Ports

The Transport layer uses ports to identify applications.

Example:

```text
192.168.1.10:443
```

Means:

```text
IP Address:
192.168.1.10

Application:
HTTPS

Port:
443
```

Common ports:

| Port | Service |
|-|-|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 389 | LDAP |
| 6443 | Kubernetes API |

---

# 🌍 3. Internet Layer

The Internet layer is responsible for:

- Logical addressing
- Routing
- Packet delivery

Main protocol:

```text
IP (Internet Protocol)
```

Versions:

## IPv4

Example:

```text
192.168.1.50
```

## IPv6

Example:

```text
2001:db8::10
```

---

# 🧭 Routing

Routing determines how packets travel from source to destination.

Example:

```text
Laptop

  |
  v

Default Gateway

  |
  v

Remote Server
```

Linux stores routing decisions in a routing table.

Command:

```bash
ip route
```

Example:

```text
default via 192.168.1.1
```

---

# 🔌 4. Network Access Layer

The Network Access layer handles communication with the physical network.

It includes:

- Network interfaces
- MAC addresses
- Ethernet
- WiFi

Linux interfaces:

```text
eth0
ens33
enp0s3
wlan0
```

Example:

```text
Application

      |

TCP Port 443

      |

IP Address

      |

MAC Address

      |

Network Interface

      |

Physical Network
```

---

# 📦 Data Flow Example

Opening a website:

```text
User enters:

https://example.com


        |
        v


DNS finds IP address


        |
        v


TCP connection established


        |
        v


HTTPS request sent


        |
        v


Web server responds
```

---

# 🐧 TCP/IP in Linux

Linux provides tools to interact with TCP/IP:

| Tool | Purpose |
|-|-|
| `ip` | Configure interfaces and routes |
| `ss` | Inspect TCP/UDP connections |
| `ping` | Test connectivity |
| `dig` | DNS queries |
| `curl` | Test application communication |
| `tcpdump` | Analyze packets |

---

# ☸️ TCP/IP and Modern Infrastructure

TCP/IP is the foundation of:

```text
🐧 Linux
   |
   v
🐳 Containers
   |
   v
☸️ Kubernetes Networking
   |
   v
☁️ Cloud Platforms
```

---

# Conclusion

TCP/IP is the language that allows systems, applications, and services to communicate.

For Linux administrators and DevOps engineers, understanding TCP/IP is essential for troubleshooting networks, securing services, and managing modern infrastructure platforms.