# 📡 Linux `ping` Command

> Testing network reachability and measuring latency between systems.

---

# 🎯 What Problem Does It Solve?

The `ping` command helps answer the most basic networking question:

> "Can my machine reach another machine?"

It is used to check:

- Network connectivity
- Packet loss
- Response time
- Basic network availability

Examples:

```bash
ping 192.168.1.10
```

or:

```bash
ping google.com
```

---

# 🧩 Basic Syntax

General syntax:

```bash
ping [options] destination
```

Examples:

```bash
ping 8.8.8.8
```

```bash
ping server.example.com
```

---

# 🔄 How Ping Works

`ping` uses:

```text
ICMP (Internet Control Message Protocol)
```

The communication flow:

```
Client

  |

  | ICMP Echo Request

  v

Server

  |

  | ICMP Echo Reply

  v

Client
```

---

# 📡 Basic Example

Command:

```bash
ping 192.168.1.20
```

Output:

```text
64 bytes from 192.168.1.20:
time=1.2 ms
```

Meaning:

```text
Host reachable

Latency:
1.2 milliseconds
```

---

# 🔢 Limit Number of Packets

By default, Linux ping runs continuously.

Use:

```bash
ping -c 4 google.com
```

Example:

```text
4 packets transmitted
4 received
0% packet loss
```

Useful for quick tests.

---

# ⏱️ Measure Latency

Example:

```bash
ping google.com
```

Output:

```text
time=25 ms
```

The value represents:

```
Round Trip Time (RTT)

Client

   |

   v

Destination

   |

   v

Client
```

---

# 🌐 Test Different Targets

## Test Local Machine

```bash
ping localhost
```

Checks:

- TCP/IP stack
- Local networking

---

## Test Gateway

Example:

```bash
ping 192.168.1.1
```

Checks:

- Local network
- Router availability

---

## Test Internet

Example:

```bash
ping 8.8.8.8
```

Checks:

- External connectivity

---

## Test DNS Name

Example:

```bash
ping google.com
```

Tests:

1. DNS resolution
2. Network connectivity

---

# 🔍 IPv4 vs IPv6

Force IPv4:

```bash
ping -4 google.com
```

Force IPv6:

```bash
ping -6 google.com
```

---

# 📦 Packet Size

Default packet size:

```text
56 bytes
```

Change size:

```bash
ping -s 1000 google.com
```

Useful for testing:

- MTU problems
- Packet fragmentation

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"No network connection"

Test:

```bash
ping 192.168.1.1
```

If it fails:

Check:

```bash
ip addr
```

and:

```bash
ip route
```

Possible causes:

- No IP address
- Interface down
- Missing route

---

## Problem 2:
"Can reach IP but not hostname"

Example:

Works:

```bash
ping 8.8.8.8
```

Fails:

```bash
ping google.com
```

Problem:

DNS issue.

Check:

```bash
cat /etc/resolv.conf
```

Test:

```bash
dig google.com
```

---

## Problem 3:
"Server is online but ping fails"

Possible reasons:

- Firewall blocks ICMP
- Host disables ping responses
- Network policy blocks traffic

Important:

> A failed ping does not always mean the server is down.

---

# 🔐 Ping and Firewalls

ICMP traffic can be controlled by firewalls.

Example:

```text
Ping Request

      |

      X

Firewall blocks ICMP

      |

      v

No response
```

On Linux:

```bash
firewall-cmd --list-all
```

---

# 🐧 Real Linux Example

Scenario:

A server cannot connect to another server.

Troubleshooting:

### 1. Check local IP

```bash
ip addr
```

### 2. Check gateway

```bash
ip route
```

### 3. Test gateway

```bash
ping 192.168.1.1
```

### 4. Test remote server

```bash
ping 192.168.1.50
```

### 5. Test application port

```bash
nc -zv 192.168.1.50 443
```

---

# ☸️ Kubernetes Connection

Ping is useful, but Kubernetes networking has some differences.

Examples:

Check node connectivity:

```bash
ping <node-ip>
```

Check Pod IP:

```bash
kubectl get pods -o wide
```

Example:

```
Pod A

10.244.1.5

    |

    v

Pod B

10.244.2.8
```

However:

- Some Kubernetes networks block ICMP
- NetworkPolicy may restrict traffic
- Application testing often uses `curl` instead

Example:

```bash
curl http://service-name:8080
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `ping host` | Test connectivity |
| `ping -c 4 host` | Send limited packets |
| `ping -4 host` | Force IPv4 |
| `ping -6 host` | Force IPv6 |
| `ping -s size host` | Change packet size |

---

# Conclusion

The `ping` command is the first tool administrators use to verify network reachability.

It helps identify whether a problem exists at the network layer before investigating services, ports, DNS, or applications.

Combined with:

```text
ip → ping → ss → curl
```

it provides a powerful troubleshooting workflow for Linux systems.