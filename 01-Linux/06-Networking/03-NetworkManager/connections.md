# 🔗 NetworkManager Connections

> Understanding the relationship between network devices and connection profiles.

---

# 🎯 What Problem Does It Solve?

NetworkManager separates:

- Physical/virtual network devices
- Network configuration profiles

This allows Linux systems to store multiple network configurations and activate them when needed.

The key concept:

```text
Device ≠ Connection
```

---

# 🌐 Device vs Connection

## Device

A device represents the actual network interface.

Examples:

```text
ens33

eth0

wlan0
```

A device is:

- Physical NIC
- Virtual NIC
- Wireless adapter

View devices:

```bash
nmcli device status
```

Example:

```text
DEVICE    TYPE      STATE

ens33     ethernet  connected

lo        loopback  unmanaged
```

---

## Connection

A connection is a configuration profile.

It contains:

- IP address
- Gateway
- DNS
- Routes
- Authentication settings

View connections:

```bash
nmcli connection show
```

Example:

```text
NAME              DEVICE

Office-LAN        ens33
```

---

# 🧩 Relationship Between Them

Think of it like this:

```
Physical Device

      |

      v

Network Interface

      |

      v

Connection Profile

      |

      +-- IP Address
      +-- Gateway
      +-- DNS
      +-- Routes
```

---

# 🔍 Example

A server has:

```
Device:

ens33


Connection:

Production-Network
```

The connection contains:

```
IP:
192.168.1.50/24


Gateway:
192.168.1.1


DNS:
192.168.1.10
```

The device simply provides the network interface.

---

# 📋 Show Devices

Command:

```bash
nmcli device
```

Example:

```text
DEVICE  TYPE      STATE

ens33   ethernet  connected
ens34   ethernet  disconnected
```

Meaning:

```text
ens33 → Active

ens34 → Available but unused
```

---

# 📋 Show Connections

Command:

```bash
nmcli connection
```

Example:

```text
NAME

Production

Backup-Network
```

A single device can have multiple connections.

Example:

```
             ens33

              |

       +------+------+

       |             |

Production     Backup

Connection     Connection

```

---

# 🔄 Activate a Connection

Example:

```bash
nmcli connection up Production
```

NetworkManager applies:

- IP configuration
- Routes
- DNS settings

to the device.

---

# ⛔ Deactivate a Connection

Command:

```bash
nmcli connection down Production
```

The profile remains saved.

Only the active configuration is removed.

---

# ➕ Create a New Connection

Example:

Create Ethernet connection:

```bash
nmcli connection add \
type ethernet \
ifname ens33 \
con-name Production
```

Now:

```bash
nmcli connection show
```

Shows:

```text
Production
```

---

# 📝 Modify a Connection

Example:

Change IP address:

```bash
nmcli connection modify Production \
ipv4.addresses 192.168.1.100/24
```

Set gateway:

```bash
nmcli connection modify Production \
ipv4.gateway 192.168.1.1
```

Set DNS:

```bash
nmcli connection modify Production \
ipv4.dns 8.8.8.8
```

---

# 🔄 Reload Connection Profiles

After modifying files:

```bash
nmcli connection reload
```

Activate:

```bash
nmcli connection up Production
```

---

# 🖥️ Real Rocky Linux Example

A server has two NICs:

```
ens33

Management Network


ens34

Storage Network
```

Create two profiles:

```
ens33

   |

Management


ens34

   |

Storage
```

Check:

```bash
nmcli device status
```

Configure:

```bash
nmcli connection show
```

Activate:

```bash
nmcli connection up Management
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
"Device exists but no network"

Check:

```bash
nmcli device status
```

Example:

```text
ens33 disconnected
```

Solution:

```bash
nmcli connection up ens33
```

---

## Problem 2:
"Wrong network profile activated"

Check:

```bash
nmcli connection show --active
```

Example:

```text
Office-Network
```

Expected:

```text
Production-Network
```

Activate correct profile:

```bash
nmcli connection up Production-Network
```

---

## Problem 3:
"Configuration changed but nothing happened"

Reload:

```bash
nmcli connection reload
```

Restart:

```bash
nmcli connection down NAME

nmcli connection up NAME
```

---

# ☸️ Kubernetes Connection

Before Kubernetes networking:

```
Node

 |

Network Device

 |

NetworkManager Connection

 |

IP Address

 |

CNI Plugin

 |

Pod Network

```

Example:

A worker node:

```
ens192

     |

192.168.252.50

     |

Kubernetes Worker

```

If the host network is wrong:

- kubelet fails
- API communication fails
- CNI fails

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `nmcli device status` | Show network devices |
| `nmcli connection show` | Show profiles |
| `nmcli connection show --active` | Show active profiles |
| `nmcli connection up` | Activate profile |
| `nmcli connection down` | Deactivate profile |
| `nmcli connection add` | Create profile |
| `nmcli connection modify` | Modify profile |
| `nmcli connection delete` | Remove profile |

---

# Conclusion

Understanding the difference between devices and connections is essential for managing Linux networking.

Devices represent the available network interfaces.

Connections represent the configuration applied to those interfaces.

NetworkManager uses this separation to provide flexible and reliable network management in modern Linux systems.