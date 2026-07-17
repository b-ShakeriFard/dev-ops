# 🛠️ NetworkManager Troubleshooting

> Diagnosing Linux network problems using NetworkManager and standard networking tools.

---

# 🎯 What Problem Does It Solve?

Network problems can happen at different layers.

A Linux administrator needs a structured approach:

- Is NetworkManager running?
- Is the interface active?
- Does the server have an IP?
- Is routing correct?
- Does DNS work?
- Are services reachable?

Random troubleshooting wastes time.

A layered approach finds problems faster.

---

# 🔎 Troubleshooting Flow

```text
NetworkManager

      |

      v

Network Device

      |

      v

IP Address

      |

      v

Routing

      |

      v

DNS

      |

      v

Ports

      |

      v

Application
```

---

# 1️⃣ Check NetworkManager Status

First check the service:

```bash
systemctl status NetworkManager
```

Expected:

```text
Active: active (running)
```

If stopped:

```bash
sudo systemctl start NetworkManager
```

Enable at boot:

```bash
sudo systemctl enable NetworkManager
```

---

# 2️⃣ Check Network Devices

Command:

```bash
nmcli device status
```

Example:

```text
DEVICE   TYPE      STATE

ens33    ethernet  connected
```

Common states:

| State | Meaning |
|-|-|
| connected | Working |
| disconnected | Available but inactive |
| unmanaged | Not controlled by NetworkManager |
| unavailable | Problem with device |

---

# 3️⃣ Check Connection Profiles

Command:

```bash
nmcli connection show
```

Example:

```text
NAME

Production-LAN
```

Check active connections:

```bash
nmcli connection show --active
```

---

# 4️⃣ Check IP Address

Use:

```bash
ip addr
```

Example:

```text
inet 192.168.1.50/24
```

Problems:

No IP:

```text
No inet address
```

Possible causes:

- DHCP failure
- Wrong connection profile
- Interface disabled

---

# 5️⃣ Check Interface Status

Command:

```bash
ip link
```

Example:

```text
ens33: UP
```

If:

```text
ens33: DOWN
```

Enable:

```bash
sudo ip link set ens33 up
```

or:

```bash
nmcli connection up ens33
```

---

# 6️⃣ Check Routing

Command:

```bash
ip route
```

Example:

```text
default via 192.168.1.1
```

A missing default route causes:

```
Local network works

but

Internet fails
```

---

# 7️⃣ Test Connectivity

Test gateway:

```bash
ping 192.168.1.1
```

Test external IP:

```bash
ping 8.8.8.8
```

Interpretation:

| Result | Possible Problem |
|-|-|
| Gateway fails | Local network issue |
| Gateway works, Internet fails | Routing problem |
| IP works, hostname fails | DNS problem |

---

# 8️⃣ Check DNS

View DNS:

```bash
nmcli device show
```

or:

```bash
cat /etc/resolv.conf
```

Test:

```bash
dig google.com
```

If:

```bash
ping 8.8.8.8
```

works but:

```bash
ping google.com
```

fails

The problem is DNS.

---

# 9️⃣ Check Listening Services

A network service must be listening.

Example:

```bash
ss -tulpn
```

Output:

```text
LISTEN :443
```

Means:

```
HTTPS service is available
```

---

# 🔟 Test Application Communication

Use:

```bash
curl
```

Example:

```bash
curl http://server:8080
```

Possible results:

Success:

```text
Application response
```

Failure:

```text
Connection refused
```

Possible causes:

- Service stopped
- Wrong port
- Firewall

---

# 🖥️ Real Rocky Linux Scenario

Problem:

```
Server cannot access internal services
```

Troubleshooting:

## Check NetworkManager

```bash
systemctl status NetworkManager
```

---

## Check interface

```bash
nmcli device status
```

---

## Check IP

```bash
ip addr
```

---

## Check gateway

```bash
ip route
```

---

## Test gateway

```bash
ping gateway-ip
```

---

## Test DNS

```bash
dig internal.company.local
```

---

## Test service

```bash
curl https://harbor.company.local
```

---

# 🚨 Common Problems

## Wrong IP Address

Check:

```bash
nmcli connection show
```

Fix:

```bash
nmcli connection modify
```

---

## No Default Gateway

Check:

```bash
ip route
```

Fix:

```bash
nmcli connection modify NAME ipv4.gateway IP
```

---

## Wrong DNS Server

Check:

```bash
cat /etc/resolv.conf
```

Fix:

```bash
nmcli connection modify NAME ipv4.dns DNS_IP
```

---

## NetworkManager Not Managing Interface

Check:

```bash
nmcli device status
```

If:

```text
unmanaged
```

Investigate:

```bash
/etc/NetworkManager/
```

---

# ☸️ Kubernetes Connection

Before troubleshooting Kubernetes networking:

First verify the Linux host:

```text
NetworkManager

      |

      v

IP Address

      |

      v

Routing

      |

      v

Kubernetes Node
```

Common Kubernetes problems caused by host networking:

- Node NotReady
- API server unreachable
- CNI failures
- DNS failures

Useful commands:

```bash
ip addr

ip route

nmcli device status

ss -tulpn
```

---

# 📚 Troubleshooting Checklist

| Question | Command |
|-|-|
| Is NetworkManager running? | `systemctl status NetworkManager` |
| Are devices connected? | `nmcli device status` |
| Is IP configured? | `ip addr` |
| Is routing correct? | `ip route` |
| Does DNS work? | `dig` |
| Is the service listening? | `ss -tulpn` |
| Does application respond? | `curl` |

---

# Conclusion

NetworkManager troubleshooting is about following a logical path from the network interface to the application.

By combining:

```text
nmcli
+
ip
+
ping
+
dig
+
ss
+
curl
```

Linux administrators can diagnose most network problems quickly and efficiently.

This troubleshooting approach is the same foundation used when debugging containers, Kubernetes nodes, and cloud infrastructure.