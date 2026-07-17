# ⚙️ Linux `nmcli` Command

> Managing Linux networking through NetworkManager.

---

# 🎯 What Problem Does It Solve?

The `nmcli` command allows administrators to configure and control network settings from the command line.

It can manage:

- 🌐 Network interfaces
- 📍 IP addresses
- 🧭 Routes
- 🔎 DNS settings
- 🔗 Connection profiles
- 📡 Wireless networks

It is the standard networking tool in:

- RHEL
- Rocky Linux
- AlmaLinux
- Fedora

---

# 🧩 Basic Syntax

General syntax:

```bash
nmcli <object> <command>
```

Common objects:

| Object | Purpose |
|-|-|
| `device` | Physical/virtual interfaces |
| `connection` | Network profiles |
| `general` | NetworkManager status |
| `networking` | Enable/disable networking |
| `radio` | Wireless control |

---

# 🔍 Check NetworkManager Status

Command:

```bash
nmcli general status
```

Example:

```text
STATE

connected
```

Meaning:

```text
NetworkManager is active
```

---

# 🌐 Show Network Devices

Command:

```bash
nmcli device status
```

Example:

```text
DEVICE   TYPE      STATE

ens33    ethernet  connected

lo       loopback  unmanaged
```

Meaning:

```text
ens33

    |

    v

Active network interface
```

---

# 📋 Show Connections

Command:

```bash
nmcli connection show
```

Example:

```text
NAME

Wired connection 1

DEVICE

ens33
```

A connection contains:

- IP address
- Gateway
- DNS
- Routes
- Security settings

---

# 🔎 Show Connection Details

Command:

```bash
nmcli connection show "Wired connection 1"
```

Example:

```text
ipv4.addresses:

192.168.1.50/24


ipv4.gateway:

192.168.1.1


ipv4.dns:

8.8.8.8
```

---

# 🔌 Activate a Connection

Bring a connection online:

```bash
nmcli connection up "Wired connection 1"
```

---

# ⛔ Deactivate a Connection

Disable:

```bash
nmcli connection down "Wired connection 1"
```

---

# 🔄 Reload NetworkManager

Reload configuration:

```bash
sudo nmcli connection reload
```

Restart NetworkManager:

```bash
sudo systemctl restart NetworkManager
```

---

# 📍 Configure Static IP

Example:

Assign IP:

```bash
sudo nmcli connection modify ens33 \
ipv4.addresses 192.168.1.100/24
```

Set gateway:

```bash
sudo nmcli connection modify ens33 \
ipv4.gateway 192.168.1.1
```

Set DNS:

```bash
sudo nmcli connection modify ens33 \
ipv4.dns 8.8.8.8
```

Enable manual addressing:

```bash
sudo nmcli connection modify ens33 \
ipv4.method manual
```

Activate:

```bash
sudo nmcli connection up ens33
```

---

# 🔎 Verify Configuration

Check with:

```bash
ip addr
```

and:

```bash
ip route
```

Check DNS:

```bash
cat /etc/resolv.conf
```

---

# 📡 Wireless Example

Show WiFi devices:

```bash
nmcli device wifi list
```

Connect:

```bash
nmcli device wifi connect SSID password PASSWORD
```

---

# 🛠️ Real Linux Example (Rocky Linux)

Scenario:

A server receives a new static IP.

Current:

```text
IP:
192.168.1.50
```

Required:

```text
IP:
192.168.1.100

Gateway:
192.168.1.1

DNS:
192.168.1.10
```

Commands:

```bash
nmcli connection show
```

Find connection:

```text
ens33
```

Modify:

```bash
nmcli con mod ens33 ipv4.addresses 192.168.1.100/24

nmcli con mod ens33 ipv4.gateway 192.168.1.1

nmcli con mod ens33 ipv4.dns 192.168.1.10

nmcli con mod ens33 ipv4.method manual
```

Apply:

```bash
nmcli con up ens33
```

Verify:

```bash
ip addr
ip route
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
"Interface has no network connection"

Check:

```bash
nmcli device status
```

Example:

```text
ens33 disconnected
```

Activate:

```bash
nmcli connection up ens33
```

---

## Problem 2:
"Wrong IP address"

Check:

```bash
nmcli connection show ens33
```

Modify:

```bash
nmcli con mod ens33 ipv4.addresses NEW_IP/24
```

Restart:

```bash
nmcli con up ens33
```

---

## Problem 3:
"DNS is not working"

Check:

```bash
nmcli connection show ens33
```

Look for:

```text
ipv4.dns
```

Test:

```bash
dig google.com
```

---

# ☸️ Kubernetes Connection

Before Kubernetes can work, nodes need correct networking.

Example:

```
Linux Node

 |

NetworkManager

 |

IP Address

 |

Default Gateway

 |

Kubernetes Node Network
```

Common checks:

```bash
nmcli device status

ip addr

ip route
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `nmcli general status` | NetworkManager status |
| `nmcli device status` | Show interfaces |
| `nmcli connection show` | Show profiles |
| `nmcli connection up` | Activate connection |
| `nmcli connection down` | Deactivate connection |
| `nmcli connection modify` | Change configuration |
| `nmcli device wifi list` | Show WiFi networks |
| `nmcli connection reload` | Reload profiles |

---

# Conclusion

`nmcli` is the primary command-line interface for NetworkManager.

It allows Linux administrators to configure and troubleshoot networking consistently across modern Enterprise Linux systems.

Understanding `nmcli` is essential for managing servers, Kubernetes nodes, and enterprise infrastructure.