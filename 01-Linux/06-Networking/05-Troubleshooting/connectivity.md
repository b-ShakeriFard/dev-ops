# 📡 Network Connectivity Troubleshooting

> Diagnosing basic network communication problems in Linux.

---

# 🎯 What Problem Does It Solve?

Connectivity troubleshooting answers:

> "Can this machine communicate with another machine?"

Before checking:

- DNS
- Applications
- Firewall
- Kubernetes services

we must verify basic network communication.

---

# 🧩 Connectivity Troubleshooting Layers

```text
Network Interface

        ↓

IP Address

        ↓

Default Gateway

        ↓

Remote Network

        ↓

Internet / External Services
```

---

# 1️⃣ Check Network Interface

First check available interfaces:

```bash
ip link
```

Example:

```text
ens33: <BROADCAST,MULTICAST,UP>
```

The interface should be:

```text
UP
```

---

## Using NetworkManager

Check devices:

```bash
nmcli device status
```

Example:

```text
DEVICE   TYPE      STATE

ens33    ethernet  connected
```

---

# 2️⃣ Check IP Address

Command:

```bash
ip addr
```

Example:

```text
inet 192.168.1.50/24
```

This tells us:

```
IP Address:

192.168.1.50


Network:

192.168.1.0/24
```

---

# ❌ No IP Address?

Example:

```text
ens33

(no inet address)
```

Possible causes:

- DHCP failure
- NetworkManager issue
- Wrong connection profile
- Interface disabled

Check:

```bash
nmcli connection show
```

Activate:

```bash
nmcli connection up <connection>
```

---

# 3️⃣ Check Default Gateway

A gateway allows communication outside the local network.

Command:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev ens33
```

Meaning:

```
Local Machine

      |

      v

Gateway

      |

      v

External Networks
```

---

# ❌ Missing Default Route

Example:

```text
192.168.1.0/24 dev ens33
```

No:

```text
default via
```

Result:

```
Local network works

Internet fails
```

Fix:

Using NetworkManager:

```bash
nmcli connection modify \
<connection> \
ipv4.gateway 192.168.1.1
```

---

# 4️⃣ Test Local Network

Test the gateway:

```bash
ping -c 4 192.168.1.1
```

Success:

```text
64 bytes from 192.168.1.1
```

Means:

✅ Interface works

✅ IP works

✅ Local network works

---

# 5️⃣ Test External IP Connectivity

Test a public IP:

```bash
ping -c 4 8.8.8.8
```

Possible results:

## Works

```
Internet connectivity exists
```

## Fails

Possible causes:

- Missing route
- Gateway problem
- Firewall
- ISP/network issue

---

# 6️⃣ Check Hostname Connectivity

Test:

```bash
ping google.com
```

If:

```bash
ping 8.8.8.8
```

works

but:

```bash
ping google.com
```

fails

The problem is probably:

```
DNS
```

Move to:

```text
dns-issues.md
```

---

# 🖥️ Real Linux Scenario

Problem:

```
Server cannot reach the internet
```

---

## Step 1: Check interface

```bash
nmcli device status
```

Result:

```
ens33 connected
```

Good.

---

## Step 2: Check IP

```bash
ip addr
```

Result:

```
192.168.1.50/24
```

Good.

---

## Step 3: Check route

```bash
ip route
```

Result:

```
default via 192.168.1.1
```

Good.

---

## Step 4: Test gateway

```bash
ping 192.168.1.1
```

Fails.

Conclusion:

Problem is between:

```
Server

 |

v

Gateway
```

Not DNS.
Not application.
Not firewall.

---

# 🔍 Useful Commands

## Show interfaces

```bash
ip link
```

---

## Show addresses

```bash
ip addr
```

---

## Show routes

```bash
ip route
```

---

## Show NetworkManager state

```bash
nmcli device status
```

---

## Show connections

```bash
nmcli connection show
```

---

# ☸️ Kubernetes Connection

Before troubleshooting Kubernetes networking:

Check the node first.

Example:

```
Kubernetes Node

        |

        v

Linux Network Interface

        |

        v

Node IP

        |

        v

Cluster Communication
```

Useful commands:

```bash
ip addr

ip route

nmcli device status

ping <node-ip>
```

Common symptoms:

## Node NotReady

Possible causes:

- Wrong IP
- Broken route
- Interface down

---

## Pod Communication Failure

Before checking CNI:

Verify:

```bash
ip route
```

and:

```bash
ip link
```

on the node.

---

# 🚨 Troubleshooting Checklist

```text
□ Network interface is UP

□ NetworkManager is running

□ IP address exists

□ Default route exists

□ Gateway responds

□ External IP responds

□ DNS resolves

□ Application responds
```

---

# Conclusion

Connectivity troubleshooting starts with the simplest question:

> "Can packets leave this machine and reach their destination?"

The correct order is:

```text
Interface
    ↓
IP Address
    ↓
Gateway
    ↓
Remote IP
    ↓
DNS
    ↓
Application
```

Mastering this workflow prevents unnecessary debugging at higher layers.