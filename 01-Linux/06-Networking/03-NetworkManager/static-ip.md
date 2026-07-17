# 📍 Configure Static IP with NetworkManager

> Assigning permanent network configuration to Linux systems using `nmcli`.

---

# 🎯 What Problem Does It Solve?

Servers usually require predictable network addresses.

Static IP configuration provides:

- Stable server identity
- Reliable DNS records
- Consistent service access
- Easier troubleshooting

Common examples:

```
Harbor Server

192.168.252.222


Kubernetes Master

192.168.1.10


LDAP Server

192.168.1.50
```

---

# 🧩 Network Configuration Components

A static network configuration usually contains:

```
IP Address

      +

Subnet Mask

      +

Gateway

      +

DNS Servers

      +

Routes
```

Example:

```
IP:

192.168.1.100/24


Gateway:

192.168.1.1


DNS:

8.8.8.8
```

---

# 🔎 Identify Network Interface

First check devices:

```bash
nmcli device status
```

Example:

```
DEVICE   TYPE      STATE

ens33    ethernet  connected
```

Our interface:

```text
ens33
```

---

# 📋 Find Connection Name

Show connections:

```bash
nmcli connection show
```

Example:

```
NAME

Wired connection 1
```

Remember:

```
Device:

ens33


Connection:

Wired connection 1
```

---

# 📍 Configure Static IP

Set IP address:

```bash
sudo nmcli connection modify \
"Wired connection 1" \
ipv4.addresses 192.168.1.100/24
```

---

# 🚪 Configure Gateway

Set default gateway:

```bash
sudo nmcli connection modify \
"Wired connection 1" \
ipv4.gateway 192.168.1.1
```

---

# 🌐 Configure DNS

Set DNS server:

```bash
sudo nmcli connection modify \
"Wired connection 1" \
ipv4.dns 8.8.8.8
```

Multiple DNS servers:

```bash
sudo nmcli connection modify \
"Wired connection 1" \
ipv4.dns "8.8.8.8 1.1.1.1"
```

---

# 🔄 Change from DHCP to Static

By default, many systems use DHCP:

```text
DHCP

automatic IP assignment
```

Change to manual:

```bash
sudo nmcli connection modify \
"Wired connection 1" \
ipv4.method manual
```

---

# 🔁 Apply Configuration

Restart the connection:

```bash
nmcli connection down "Wired connection 1"

nmcli connection up "Wired connection 1"
```

Or:

```bash
nmcli connection reload
```

---

# ✅ Verify Configuration

Check IP:

```bash
ip addr
```

Expected:

```
inet 192.168.1.100/24
```

---

Check route:

```bash
ip route
```

Expected:

```
default via 192.168.1.1
```

---

Check DNS:

```bash
cat /etc/resolv.conf
```

---

Test gateway:

```bash
ping 192.168.1.1
```

---

Test Internet:

```bash
ping 8.8.8.8
```

---

Test DNS:

```bash
dig google.com
```

---

# 🖥️ Real Rocky Linux Example

Scenario:

A new Kubernetes worker node:

```
Hostname:

worker01


Interface:

ens192


Required IP:

192.168.252.50/24


Gateway:

192.168.252.1


DNS:

192.168.252.10
```

Commands:

Find connection:

```bash
nmcli connection show
```

Configure:

```bash
nmcli con mod ens192 \
ipv4.addresses 192.168.252.50/24

nmcli con mod ens192 \
ipv4.gateway 192.168.252.1

nmcli con mod ens192 \
ipv4.dns 192.168.252.10

nmcli con mod ens192 \
ipv4.method manual
```

Activate:

```bash
nmcli con up ens192
```

Verify:

```bash
ip addr

ip route

dig internal.domain
```

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"IP address did not change"

Check:

```bash
nmcli connection show
```

Verify:

```bash
ipv4.method manual
```

Restart:

```bash
nmcli con down NAME

nmcli con up NAME
```

---

## Problem 2:
"Server has IP but no Internet"

Check route:

```bash
ip route
```

Look for:

```
default via gateway
```

No default route:

```text
Network knows local devices only
```

---

## Problem 3:
"IP works but hostname does not"

Test:

```bash
ping 8.8.8.8
```

Works.

Then:

```bash
dig google.com
```

Fails.

Problem:

DNS configuration.

Check:

```bash
nmcli connection show NAME
```

---

# ☸️ Kubernetes Connection

Kubernetes nodes require stable networking.

Example:

```
Worker Node

        |

        v

Static IP

192.168.252.50

        |

        v

kubelet

        |

        v

Kubernetes Cluster
```

Problems caused by bad IP configuration:

- Node becomes NotReady
- API server unreachable
- CNI failures
- DNS failures

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `nmcli device status` | Find interfaces |
| `nmcli connection show` | Find profiles |
| `nmcli con mod` | Modify configuration |
| `nmcli con up` | Apply configuration |
| `ip addr` | Verify IP |
| `ip route` | Verify gateway |
| `dig` | Verify DNS |
| `ping` | Test connectivity |

---

# Conclusion

Static IP configuration is a fundamental Linux administration skill.

Using NetworkManager and `nmcli`, administrators can reliably configure servers for enterprise environments, containers, and Kubernetes infrastructure.