# 🌐 Linux `ip` Command

> The primary tool for viewing and managing Linux networking.

---

# 🎯 What Problem Does It Solve?

The `ip` command allows administrators to:

- View network interfaces
- Check IP addresses
- Enable or disable interfaces
- Manage routing tables
- Configure network settings

It replaces older tools such as:

```bash
ifconfig
route
```

Modern Linux distributions use:

```bash
iproute2
```

as the standard networking toolkit.

---

# 🧩 Basic Syntax

The general syntax:

```bash
ip <object> <command>
```

Common objects:

| Object | Purpose |
|-|-|
| `addr` | IP addresses |
| `link` | Network interfaces |
| `route` | Routing table |
| `neigh` | ARP/neighbour table |

---

# 📍 View IP Addresses

## Basic command

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

## Short version

```bash
ip a
```

Same as:

```bash
ip addr
```

---

# 🔌 View Network Interfaces

Command:

```bash
ip link
```

Example:

```text
1: lo: <LOOPBACK,UP>
2: ens33: <BROADCAST,MULTICAST,UP>
```

Shows:

- Interface name
- Status
- Hardware information

---

# ⬆️ Enable / Disable Interfaces

Enable:

```bash
sudo ip link set ens33 up
```

Disable:

```bash
sudo ip link set ens33 down
```

Check:

```bash
ip link show ens33
```

---

# ➕ Add an IP Address

Temporary IP assignment:

```bash
sudo ip addr add 192.168.1.100/24 dev ens33
```

Verify:

```bash
ip addr show ens33
```

⚠️ Important:

This change is temporary.

After reboot:

```text
Configuration disappears
```

Permanent configuration depends on:

- NetworkManager
- netplan
- systemd-networkd

---

# ➖ Remove an IP Address

Command:

```bash
sudo ip addr del 192.168.1.100/24 dev ens33
```

---

# 🧭 View Routing Table

Command:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev ens33

192.168.1.0/24 dev ens33
```

Meaning:

```
Unknown destination

        |

        v

Default Gateway

        |

        v

External Network
```

---

# 🚪 Default Gateway

Show only default route:

```bash
ip route show default
```

Example:

```text
default via 192.168.1.1
```

The gateway is where Linux sends traffic that does not belong to the local network.

---

# ➕ Add a Route

Example:

```bash
sudo ip route add 10.10.0.0/16 via 192.168.1.1
```

Meaning:

```text
Destination:

10.10.0.0/16


Gateway:

192.168.1.1
```

---

# 🗑️ Delete a Route

```bash
sudo ip route del 10.10.0.0/16
```

---

# 🖥️ Real Linux Example

Imagine a server:

```
Interface:

ens33


IP:

192.168.10.50/24


Gateway:

192.168.10.1
```

Commands:

Check IP:

```bash
ip addr show ens33
```

Check route:

```bash
ip route
```

Test gateway:

```bash
ping 192.168.10.1
```

Test Internet:

```bash
ping 8.8.8.8
```

---

# 🔍 Troubleshooting Scenarios

## Problem 1:
"My server has no network connection."

Check:

```bash
ip link
```

Possible issue:

```text
ens33: DOWN
```

Fix:

```bash
sudo ip link set ens33 up
```

---

## Problem 2:
"I can reach my local network but not the Internet."

Check route:

```bash
ip route
```

Look for:

```text
default via x.x.x.x
```

No default route means Linux does not know where to send external traffic.

---

## Problem 3:
"Wrong IP address assigned."

Check:

```bash
ip addr
```

Remove:

```bash
sudo ip addr del OLD_IP/24 dev ens33
```

Add:

```bash
sudo ip addr add NEW_IP/24 dev ens33
```

---

# ☸️ Kubernetes Connection

The `ip` command is extremely useful when troubleshooting Kubernetes nodes.

Examples:

Check node IP:

```bash
ip addr
```

Check routes:

```bash
ip route
```

Inspect container networking:

```bash
ip link
```

Kubernetes networking:

```
Node IP

    |

    v

CNI Interface

    |

    v

Pod Network

    |

    v

Service Network
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `ip addr` | Show IP addresses |
| `ip link` | Show interfaces |
| `ip route` | Show routing table |
| `ip neigh` | Show ARP/neighbour table |
| `ip addr add` | Add IP address |
| `ip addr del` | Remove IP address |
| `ip link set up` | Enable interface |
| `ip link set down` | Disable interface |
| `ip route add` | Add route |
| `ip route del` | Remove route |

---

# Conclusion

The `ip` command is the foundation of Linux network administration.

It provides visibility and control over interfaces, addresses, and routing — the essential building blocks of Linux networking.

A strong understanding of `ip` is required for troubleshooting servers, containers, and Kubernetes networking.