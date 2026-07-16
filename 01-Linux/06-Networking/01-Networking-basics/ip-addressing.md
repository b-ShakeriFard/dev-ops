# 📍 Linux IP Addressing

> Managing and troubleshooting IP addresses on Linux systems.

---

#### 🛠️ IP Addressing Command Cheat Sheet

| Command | Purpose | Example | Description |
|---|---|---|---|
| `ip addr` | Show IP addresses | `ip addr` | Display all network interfaces and assigned IP addresses |
| `ip addr show <interface>` | Show specific interface details | `ip addr show ens33` | Display IP configuration for a specific interface |
| `ip link` | Show network interfaces | `ip link` | Display interfaces and their status (UP/DOWN) |
| `ip link set <interface> up` | Enable interface | `sudo ip link set ens33 up` | Activate a network interface |
| `ip link set <interface> down` | Disable interface | `sudo ip link set ens33 down` | Disable a network interface |
| `ip addr add` | Add IP address | `sudo ip addr add 192.168.1.100/24 dev ens33` | Temporarily assign an IP address to an interface |
| `ip addr del` | Remove IP address | `sudo ip addr del 192.168.1.100/24 dev ens33` | Remove an assigned IP address |
| `ip route` | Show routing table | `ip route` | Display routes and default gateway |
| `ip route add` | Add route | `sudo ip route add 10.10.0.0/16 via 192.168.1.1` | Add a static route |
| `ip route del` | Remove route | `sudo ip route del 10.10.0.0/16` | Remove a static route |
| `ping` | Test connectivity | `ping 192.168.1.1` | Check whether another host is reachable |
| `ping -c` | Send limited packets | `ping -c 4 google.com` | Send a specific number of ICMP packets |
| `traceroute` | Trace packet path | `traceroute google.com` | Show the path packets take to a destination |
| `dig` | DNS query | `dig google.com` | Query DNS records and resolve names |
| `nslookup` | DNS lookup | `nslookup google.com` | Simple DNS troubleshooting tool |
| `hostname -I` | Show host IP | `hostname -I` | Display assigned IP addresses |
| `curl` | Test application connectivity | `curl https://example.com` | Test HTTP/HTTPS communication |
| `ss -tuln` | Show listening ports | `ss -tuln` | Display TCP/UDP ports waiting for connections |
| `ss -tulpn` | Show ports and processes | `sudo ss -tulpn` | Show which processes are using network ports |
| `tcpdump` | Capture packets | `sudo tcpdump -i ens33` | Analyze network traffic at packet level |
| `tcpdump port` | Capture specific port traffic | `sudo tcpdump -i ens33 port 53` | Capture traffic for a specific port |

---

### 🔎 View IP Addresses

The main command for IP configuration is:

```bash
ip addr
```

Example:

```text
2: ens33:
    inet 192.168.1.50/24
```

Meaning:

```text
Interface:
ens33

IP Address:
192.168.1.50

Network:
192.168.1.0/24
```

---

# 🌐 IPv4 Address Format

Example:

```text
192.168.1.50
```

An IPv4 address contains:

```text
Network Part + Host Part
```

Example:

```text
192.168.1.50/24

Network:
192.168.1.0

Host:
50
```

The `/24` is the subnet size.

---

# 📏 CIDR Notation

CIDR defines how many bits belong to the network.

Common examples:

| CIDR | Subnet Mask | Hosts |
|-|-|-|
| `/8` | 255.0.0.0 | ~16 million |
| `/16` | 255.255.0.0 | ~65 thousand |
| `/24` | 255.255.255.0 | 254 |

Example:

```text
192.168.10.25/24
```

Means:

```text
Network:
192.168.10.0

Available hosts:
192.168.10.1 - 192.168.10.254
```

---

# 🖥️ Show Specific Interface

Example:

```bash
ip addr show ens33
```

Output:

```text
inet 192.168.1.50/24
```

Useful when a server has multiple interfaces.

Example:

```text
ens33 → Management Network

ens34 → Storage Network
```

---

# ➕ Add an IP Address

Temporary IP assignment:

```bash
sudo ip addr add 192.168.1.100/24 dev ens33
```

Check:

```bash
ip addr show ens33
```

⚠️ This change disappears after reboot.

Permanent configuration depends on the Linux distribution.

---

# ➖ Remove an IP Address

Remove an address:

```bash
sudo ip addr del 192.168.1.100/24 dev ens33
```

---

# 🔌 Enable / Disable Interface

Show interfaces:

```bash
ip link
```

Example:

```text
ens33: DOWN
```

Enable:

```bash
sudo ip link set ens33 up
```

Disable:

```bash
sudo ip link set ens33 down
```

---

# 🧭 Default Gateway

The gateway is where traffic goes when the destination is outside the local network.

View routes:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev ens33
```

Meaning:

```text
Unknown destination

        |

        v

Gateway 192.168.1.1

        |

        v

External Network
```

---

# ➕ Add a Route

Example:

```bash
sudo ip route add 10.10.0.0/16 via 192.168.1.1
```

Meaning:

```text
Network:
10.10.0.0/16

Gateway:
192.168.1.1
```

---

# 🌍 Public vs Private IP

Check your local IP:

```bash
ip addr
```

Private examples:

```text
10.x.x.x

172.16.x.x - 172.31.x.x

192.168.x.x
```

Used inside:

- Home networks
- Enterprise networks
- Data centers

---

# 🔄 Check Connectivity

Test another machine:

```bash
ping 192.168.1.20
```

Example:

```text
64 bytes from 192.168.1.20
time=1 ms
```

Test gateway:

```bash
ping 192.168.1.1
```

Test Internet:

```bash
ping 8.8.8.8
```

---

# 🔍 DNS vs IP Address

IP address:

```text
142.250.x.x
```

Human-friendly name:

```text
google.com
```

Resolve DNS:

```bash
dig google.com
```

or:

```bash
nslookup google.com
```

---

# 🛠️ Useful IP Troubleshooting Commands

## Show addresses

```bash
ip addr
```

## Show routes

```bash
ip route
```

## Show interfaces

```bash
ip link
```

## Test connectivity

```bash
ping
```

## Trace packet path

```bash
traceroute google.com
```

## Check DNS resolution

```bash
dig
```

---

# 🐧 Linux Networking Example

A typical Linux server:

```text
Interface:

ens33

        |

        v

IP Address:

192.168.1.50/24

        |

        v

Gateway:

192.168.1.1

        |

        v

Internet / Other Networks
```

Commands:

```bash
ip addr
ip route
ping
dig
```

---

# ☸️ Kubernetes Connection

Linux IP concepts directly appear in Kubernetes:

```text
Linux Host IP

        |

        v

Node IP

        |

        v

Pod IP

        |

        v

Service IP
```

Understanding Linux IP addressing makes Kubernetes networking much easier to understand.

---

# Conclusion

Linux administrators use IP addressing commands every day to configure interfaces, understand network topology, and troubleshoot connectivity problems.

The `ip` command is the central tool for managing Linux networking.
