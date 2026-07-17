# 🕵️ Linux `tcpdump` Command

> Capturing and analyzing network packets on Linux systems.

---

# 🎯 What Problem Does It Solve?

The `tcpdump` command helps administrators answer:

> "What is actually happening on the network?"

It provides visibility into:

- Incoming packets
- Outgoing packets
- TCP connections
- DNS queries
- HTTP traffic
- Network errors

When tools like:

```bash
ping
ss
curl
```

do not explain the problem, `tcpdump` shows the actual traffic.

---

# 🧩 Basic Syntax

General syntax:

```bash
tcpdump [options] [filter]
```

Example:

```bash
sudo tcpdump
```

By default, it captures traffic from available interfaces.

---

# 🔌 Select Network Interface

First, list interfaces:

```bash
tcpdump -D
```

Example:

```text
1. ens33
2. lo
3. docker0
```

Capture from a specific interface:

```bash
sudo tcpdump -i ens33
```

---

# 👀 Basic Packet Capture

Command:

```bash
sudo tcpdump -i ens33
```

Example output:

```text
192.168.1.10 > 192.168.1.20

TCP 443

SYN
```

Meaning:

```text
Client

192.168.1.10

starts connection

to:

Server

192.168.1.20:443
```

---

# 🔢 Limit Number of Packets

By default, tcpdump keeps running.

Capture only 10 packets:

```bash
sudo tcpdump -i ens33 -c 10
```

Useful for quick tests.

---

# 🌐 Capture Specific Port

## HTTP Traffic

```bash
sudo tcpdump -i ens33 port 80
```

---

## HTTPS Traffic

```bash
sudo tcpdump -i ens33 port 443
```

---

## DNS Traffic

```bash
sudo tcpdump -i ens33 port 53
```

Example:

```text
Client

 |

DNS Query

 |

DNS Server
```

---

# 🖥️ Capture Traffic from a Host

Example:

```bash
sudo tcpdump host 192.168.1.50
```

Only captures traffic involving:

```text
192.168.1.50
```

---

# 📡 Capture TCP Traffic

Example:

```bash
sudo tcpdump tcp
```

Only TCP packets are shown.

---

# 💾 Save Packets to a File

Capture:

```bash
sudo tcpdump -i ens33 -w capture.pcap
```

The file:

```text
capture.pcap
```

can be opened with:

- Wireshark
- tshark

---

# 📂 Read a Capture File

Example:

```bash
tcpdump -r capture.pcap
```

Useful when analyzing:

- Production incidents
- Network problems
- Security events

---

# 🔍 Verbose Output

More details:

```bash
sudo tcpdump -v
```

Even more:

```bash
sudo tcpdump -vvv
```

Shows:

- Packet headers
- Protocol details
- Flags

---

# 🚦 TCP Connection Example

A TCP connection begins with:

```
Client

  |

  | SYN

  v

Server


  |

  | SYN-ACK

  v


Client

  |

  | ACK

  v

Connection Established
```

Capture:

```bash
sudo tcpdump tcp
```

You can observe this handshake.

---

# 🛠️ Real Linux Examples

## Example 1:
"Application cannot connect to database"

Application:

```text
192.168.1.10
```

Database:

```text
192.168.1.20:5432
```

Capture:

```bash
sudo tcpdump host 192.168.1.20 and port 5432
```

You can see:

- Connection attempts
- Responses
- Failures

---

## Example 2:
"DNS is slow"

Capture DNS:

```bash
sudo tcpdump port 53
```

Observe:

```text
DNS Request

      |

      v

DNS Response
```

---

## Example 3:
"HTTPS connection fails"

Capture:

```bash
sudo tcpdump port 443
```

Check:

- TCP handshake
- TLS packets
- Connection resets

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"Service is listening but clients cannot connect"

Check:

```bash
ss -tulpn
```

Shows:

```text
LISTEN :8080
```

Still failing?

Capture:

```bash
sudo tcpdump port 8080
```

Questions:

- Are packets arriving?
- Is the server responding?

---

## Problem 2:
"Firewall problem?"

Capture traffic:

```bash
sudo tcpdump host client-ip
```

If:

```
Request arrives

but

No response leaves
```

Possible causes:

- Firewall
- Application failure
- Network policy

---

# ☸️ Kubernetes Connection

`tcpdump` is extremely useful for Kubernetes networking.

Examples:

Capture traffic on a node:

```bash
sudo tcpdump -i any
```

Investigate:

- Pod communication
- Service traffic
- CNI problems
- NetworkPolicy issues

Traffic flow:

```
Pod

 |

CNI Interface

 |

Linux Network Stack

 |

Physical Network
```

---

# 🐳 Container Networking

Docker/Podman example:

List interfaces:

```bash
ip link
```

You may see:

```text
docker0
cni0
```

Capture container traffic:

```bash
sudo tcpdump -i docker0
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `tcpdump -D` | List interfaces |
| `tcpdump -i eth0` | Capture interface traffic |
| `tcpdump port 80` | Capture HTTP |
| `tcpdump port 53` | Capture DNS |
| `tcpdump host IP` | Capture host traffic |
| `tcpdump tcp` | Capture TCP packets |
| `tcpdump -c 10` | Capture limited packets |
| `tcpdump -w file.pcap` | Save capture |
| `tcpdump -r file.pcap` | Read capture |

---

# Conclusion

`tcpdump` provides packet-level visibility into Linux networking.

It is the final troubleshooting layer:

```text
Configuration
      |
Connectivity
      |
Services
      |
Applications
      |
Packets
```

For Linux administrators and DevOps engineers, understanding `tcpdump` is essential for diagnosing complex networking issues in servers, containers, and Kubernetes environments.