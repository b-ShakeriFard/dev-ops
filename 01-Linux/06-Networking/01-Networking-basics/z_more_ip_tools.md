# 🛠️ Linux Network Commands

> Essential tools for configuring, testing, and troubleshooting Linux networking.

---

## 🌐 `ip` — Network Interfaces and Routes

The `ip` command is the modern tool for managing Linux networking.

### Show network interfaces

```bash
ip addr
```

Example:

```text
ens33:
    inet 192.168.1.50/24
```

Shows:

- Network interface
- IP address
- Subnet information

---

### Show routing table

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev ens33
```

Meaning:

```text
All external traffic
        |
        v
Gateway 192.168.1.1
        |
        v
Interface ens33
```

---

### Show interface status

```bash
ip link
```

Example:

```text
ens33: UP
```

---

# 🔌 `ss` — Inspect TCP/UDP Connections

The `ss` command displays network sockets.

It helps answer:

> "Which services are listening and who is connected?"

---

### Show listening ports

```bash
ss -tuln
```

Example:

```text
tcp   LISTEN   0.0.0.0:22
tcp   LISTEN   0.0.0.0:443
```

Meaning:

```text
Port 22  → SSH
Port 443 → HTTPS
```

---

### Show processes using ports

```bash
sudo ss -tulpn
```

Example:

```text
LISTEN 0.0.0.0:6443

users:
kube-apiserver
```

Useful for Kubernetes troubleshooting.

---

# 📡 `ping` — Test Connectivity

`ping` tests whether a host can be reached using ICMP packets.

Example:

```bash
ping google.com
```

Output:

```text
64 bytes from google.com
time=20 ms
```

Useful for checking:

- Network availability
- Latency
- Packet loss

Example:

```bash
ping 192.168.1.10
```

---

# 🔎 `dig` — DNS Queries

The `dig` command investigates DNS resolution.

Example:

```bash
dig example.com
```

Output:

```text
ANSWER SECTION:

example.com

93.184.216.34
```

Meaning:

```text
Hostname

    |

    v

IP Address
```

---

### Query a specific DNS server

```bash
dig @8.8.8.8 example.com
```

Useful for:

- DNS troubleshooting
- Checking DNS propagation
- Debugging Kubernetes CoreDNS

---

# 🌍 `curl` — Test Application Communication

`curl` tests communication with application services.

Commonly used for:

- HTTP
- HTTPS
- REST APIs

---

### Test a web server

```bash
curl https://example.com
```

Returns:

```html
<html>
Welcome
</html>
```

---

### Show HTTP headers

```bash
curl -I https://example.com
```

Example:

```text
HTTP/2 200

server: nginx
```

---

### Test a local application

```bash
curl http://localhost:8080
```

Useful for testing:

- Web applications
- APIs
- Kubernetes services

---

# 🕵️ `tcpdump` — Packet Analysis

`tcpdump` captures network packets directly from interfaces.

It is used for deep troubleshooting.

---

### Capture traffic

```bash
sudo tcpdump -i ens33
```

Example:

```text
192.168.1.10 > 192.168.1.20 TCP SYN
```

Shows:

```text
Client

  |

TCP connection request

  |

Server
```

---

### Capture HTTP traffic

```bash
sudo tcpdump -i ens33 port 80
```

---

### Capture DNS traffic

```bash
sudo tcpdump -i ens33 port 53
```

---

# 🚦 Troubleshooting Workflow

A common troubleshooting approach:

```text
1. Check interface
        |
        v
     ip addr

2. Check connectivity
        |
        v
      ping

3. Check DNS
        |
        v
      dig

4. Check ports
        |
        v
       ss

5. Check application
        |
        v
      curl

6. Analyze packets
        |
        v
    tcpdump
```

---

# 🐧 Commands Summary

| Command | Purpose |
|---|---|
| `ip` | Configure interfaces and routes |
| `ss` | Inspect TCP/UDP connections |
| `ping` | Test connectivity |
| `dig` | DNS queries |
| `curl` | Test application communication |
| `tcpdump` | Analyze packets |

---

# Conclusion

These networking commands are essential tools for every Linux administrator.

They provide visibility into:

- Network configuration
- Connectivity
- DNS resolution
- Services
- Application communication
- Packet-level behavior

Mastering these commands provides the foundation required for troubleshooting modern environments such as containers, Kubernetes, and cloud platforms.
