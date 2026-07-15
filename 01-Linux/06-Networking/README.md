# 🌐 Linux Networking

> Connecting Linux systems, services, and applications through modern network technologies.

---

## Overview

Networking is a fundamental component of Linux system administration.

Linux servers communicate through networks to provide services, exchange data, access storage, and interact with other systems.

A Linux administrator must understand how networking works, how to configure interfaces, troubleshoot connectivity issues, and secure communication between systems.

Strong networking knowledge is essential for modern infrastructure platforms such as containers, Kubernetes, cloud environments, and enterprise systems.

---

# 🧩 Linux Networking Fundamentals

Linux networking is built on the TCP/IP model.

Key concepts:

| Concept | Description |
|-|-|
| 🌐 IP Address | Unique identity of a network interface |
| 🔌 Interface | Hardware or virtual network connection |
| 📡 Protocol | Rules for communication |
| 🚪 Port | Endpoint for applications |
| 🧭 Routing | Path selection between networks |
| 🏷️ DNS | Name resolution system |
| 🔥 Firewall | Network traffic control |

---

# 🖥️ Network Interfaces

Linux communicates through network interfaces.

Examples:

```text
eth0
ens33
enp0s3
wlan0
```

Interfaces can be:

- Physical Ethernet adapters
- Wireless adapters
- Virtual interfaces
- Container bridges

Administrators manage interfaces to:

- Configure IP addresses
- Enable/disable connections
- Troubleshoot connectivity

---

# 📍 IP Addressing

Linux systems use IP addresses to communicate.

Two major versions:

## IPv4

Example:

```text
192.168.1.100
```

## IPv6

Example:

```text
2001:db8::1
```

Important concepts:

- Address
- Subnet
- Gateway
- Network mask
- Broadcast address

---

# 🛣️ Routing

Routing determines where network traffic should go.

A Linux system uses routing tables to decide:

- Local network communication
- Remote network communication
- Default gateway selection

Example:

```bash
ip route
```

---

# 🔎 DNS and Name Resolution

DNS converts names into IP addresses.

Example:

```text
google.com

      ↓

142.250.x.x
```

Linux name resolution involves:

```text
/etc/hosts

/etc/resolv.conf

DNS servers
```

---

# 🔐 Network Security

Linux provides multiple layers of network security:

## Firewall

Controls incoming and outgoing traffic.

Examples:

- firewalld
- nftables
- iptables

## Secure Communication

Common technologies:

- SSH
- TLS
- VPN

---

# 🛠️ Essential Networking Commands

Linux administrators use many tools:

| Command | Purpose |
|-|-|
| `ip` | Network configuration |
| `ss` | Socket inspection |
| `ping` | Connectivity testing |
| `traceroute` | Path analysis |
| `dig` | DNS queries |
| `nslookup` | DNS troubleshooting |
| `curl` | HTTP communication |
| `wget` | Download resources |
| `nmcli` | NetworkManager control |
| `tcpdump` | Packet analysis |

---

# ⚙️ Network Services

Linux servers commonly provide network services:

Examples:

```text
🌐 Web Server
   nginx / Apache

📂 File Sharing
   NFS / Samba

🔑 Authentication
   LDAP / Kerberos

📦 Container Networking
   Docker / Podman

☸️ Cluster Networking
   Kubernetes CNI
```

---

# 🏢 Enterprise Linux Networking

Enterprise environments require advanced networking concepts:

- Static addressing
- VLANs
- Bonding
- DNS integration
- Firewall policies
- Network troubleshooting
- High availability networking

Tools commonly used:

```text
NetworkManager
nmcli
firewalld
iproute2
```

---

# Conclusion

Linux networking provides the foundation for communication between systems, applications, and infrastructure platforms.

A Linux administrator who understands networking can troubleshoot problems faster, secure systems effectively, and build reliable environments for modern technologies such as containers, Kubernetes, and cloud platforms.

---

