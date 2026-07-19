# 🧭 Routing Troubleshooting

> Diagnosing Linux routing problems using `ip route` and related tools.

---

# 🎯 What Problem Does It Solve?

Routing determines where network packets should go.

The key question:

> "Which path should this packet take?"

Example:

```
Linux Server

192.168.1.50

      |

      v

Default Gateway

192.168.1.1

      |

      v

Internet
```

Without correct routing:

- Local network may work
- External communication fails

---

# 🧩 Routing Components

A Linux route contains:

```
Destination Network

        +

Gateway

        +

Interface
```

Example:

```
default via 192.168.1.1 dev ens33
```

Meaning:

```
Unknown destinations

        |

        v

Gateway 192.168.1.1

        |

        v

Interface ens33
```

---

# 1️⃣ View Routing Table

Command:

```bash
ip route
```

Example:

```
default via 192.168.1.1 dev ens33

192.168.1.0/24 dev ens33
```

Explanation:

```
192.168.1.0/24

↓

Local network


default

↓

Everything else
```

---

# 2️⃣ Check Default Route

The default route is used when no specific route exists.

Command:

```bash
ip route | grep default
```

Example:

```
default via 192.168.1.1 dev ens33
```

---

# ❌ Missing Default Gateway

Example:

```
192.168.1.0/24 dev ens33
```

Problem:

```
Local machines reachable

Internet unreachable
```

Fix using NetworkManager:

```bash
nmcli connection modify \
<connection> \
ipv4.gateway 192.168.1.1
```

Activate:

```bash
nmcli connection up <connection>
```

---

# 3️⃣ Check Route to a Specific Destination

Command:

```bash
ip route get DESTINATION
```

Example:

```bash
ip route get 8.8.8.8
```

Output:

```
8.8.8.8 via 192.168.1.1 dev ens33
```

This shows the exact path Linux will use.

---

# 4️⃣ Add a Temporary Route

Example:

Reach network:

```
10.10.0.0/24
```

through gateway:

```
192.168.1.254
```

Command:

```bash
sudo ip route add \
10.10.0.0/24 \
via 192.168.1.254
```

Check:

```bash
ip route
```

Note:

This disappears after reboot.

---

# 5️⃣ Persistent Routes

For Enterprise Linux:

Use NetworkManager.

Example:

```bash
nmcli connection modify ens33 \
+ipv4.routes "10.10.0.0/24 192.168.1.254"
```

Apply:

```bash
nmcli connection up ens33
```

Verify:

```bash
ip route
```

---

# 6️⃣ Test Routing

First test gateway:

```bash
ping 192.168.1.1
```

Then external IP:

```bash
ping 8.8.8.8
```

Interpretation:

| Result | Problem |
|-|-|
| Gateway fails | Local network issue |
| Gateway works, external fails | Routing problem |
| External IP works, hostname fails | DNS problem |

---

# 7️⃣ Trace Network Path

Command:

```bash
traceroute google.com
```

or:

```bash
tracepath google.com
```

Example:

```
Server

 |

Router 1

 |

Router 2

 |

Destination
```

Useful for finding:

- Broken routes
- Network hops
- Delays

---

# 🖥️ Real Linux Scenario

Problem:

```
Server can access local machines

but cannot access Internet
```

---

## Check IP

```bash
ip addr
```

Result:

```
192.168.1.50/24
```

Good.

---

## Check Route

```bash
ip route
```

Result:

```
192.168.1.0/24 dev ens33
```

Missing:

```
default via
```

---

## Fix

```bash
nmcli con mod ens33 \
ipv4.gateway 192.168.1.1
```

Activate:

```bash
nmcli con up ens33
```

Test:

```bash
ping 8.8.8.8
```

---

# 🚨 Common Routing Problems

## Problem 1:
## Wrong Gateway

Check:

```bash
ip route
```

Example:

```
default via 192.168.10.1
```

but network uses:

```
192.168.1.1
```

Fix:

Update gateway.

---

## Problem 2:
## Multiple Network Interfaces

Example:

```
ens33

192.168.1.50


ens34

10.10.10.50
```

Linux must choose the correct path.

Check:

```bash
ip route
```

---

## Problem 3:
## Asymmetric Routing

Traffic goes:

```
Request

Server A → Router 1
```

but returns:

```
Router 2 → Server A
```

Results:

- Connection failures
- Timeouts

Common in:

- Multi-homed servers
- Kubernetes nodes
- Storage networks

---

# ☸️ Kubernetes Connection

Routing is fundamental to Kubernetes networking.

Example:

```
Node Network

      |

      v

CNI Routing

      |

      v

Pod Network

      |

      v

Service Network
```

Useful commands:

On nodes:

```bash
ip route
```

Check interfaces:

```bash
ip link
```

Check CNI:

```bash
ip addr
```

---

# 🏢 Enterprise Examples

## Kubernetes

Common networks:

```
Node Network

192.168.x.x


Pod Network

10.244.x.x


Service Network

10.96.x.x
```

Routing connects these networks.

---

## OpenShift

Common checks:

```bash
ip route

ip addr

nmcli device status
```

Incorrect routing can cause:

- Node NotReady
- API communication failures
- CNI problems

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `ip route` | Show routing table |
| `ip route get IP` | Show path to destination |
| `ip route add` | Add temporary route |
| `nmcli connection modify` | Add persistent route |
| `ping gateway` | Test gateway |
| `tracepath` | Trace path |
| `traceroute` | Show network hops |

---

# Conclusion

Routing determines how Linux sends traffic between networks.

The troubleshooting order is:

```
Interface

    ↓

IP Address

    ↓

Route Table

    ↓

Gateway

    ↓

Destination
```

A correct IP address does not guarantee connectivity.

The system also needs a correct path to the destination.