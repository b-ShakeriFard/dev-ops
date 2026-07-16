# 🔌 Linux `ss` Command

> Inspecting TCP and UDP sockets, listening services, and active connections.

---

# 🎯 What Problem Does It Solve?

The `ss` command helps administrators understand network communication on a Linux system.

It answers questions like:

- Which ports are open?
- Which services are listening?
- Who is connected to my server?
- Which process owns a port?
- Is my application accepting connections?

Example question:

> "My web application is not reachable. Is nginx actually running and listening?"

Solution:

```bash
ss -tulpn
```

---

# 🧩 Basic Syntax

General syntax:

```bash
ss [options]
```

Common options:

| Option | Purpose |
|-|-|
| `-t` | TCP sockets |
| `-u` | UDP sockets |
| `-l` | Listening sockets |
| `-n` | Do not resolve names |
| `-p` | Show processes |
| `-a` | Show all sockets |

---

# 👀 Show Listening Ports

Command:

```bash
ss -tuln
```

Example:

```text
Netid State   Local Address

tcp   LISTEN  0.0.0.0:22

tcp   LISTEN  0.0.0.0:443
```

Meaning:

```text
Port 22

    |
    v

SSH server


Port 443

    |
    v

HTTPS server
```

---

# 🔎 Show Processes Using Ports

Command:

```bash
sudo ss -tulpn
```

Example:

```text
tcp LISTEN

0.0.0.0:6443

users:
("kube-apiserver")
```

Meaning:

```text
Port 6443

      |

      v

Kubernetes API Server
```

---

# 🌐 Show Active Connections

Command:

```bash
ss -t
```

Example:

```text
ESTAB

192.168.1.50:50000

        |

        v

192.168.1.20:443
```

Meaning:

```text
Client

192.168.1.50

      |

      |

Server

192.168.1.20:443
```

---

# 🚚 TCP Connections

Show TCP only:

```bash
ss -t
```

Example:

```text
ESTAB
```

Common TCP states:

| State | Meaning |
|-|-|
| LISTEN | Waiting for connections |
| ESTABLISHED | Active connection |
| TIME-WAIT | Connection recently closed |
| CLOSE-WAIT | Waiting for application |

---

# 📡 UDP Connections

Show UDP sockets:

```bash
ss -u
```

Example:

```text
UNCONN

0.0.0.0:53
```

Meaning:

```text
DNS service listening on UDP port 53
```

---

# 🔍 Find a Specific Port

Example:

Check HTTPS:

```bash
ss -tuln | grep 443
```

Output:

```text
LISTEN 0.0.0.0:443
```

---

Check Kubernetes API:

```bash
ss -tuln | grep 6443
```

---

# 🖥️ Real Linux Example

A web server:

```
Client

   |

TCP 443

   |

nginx

   |

Linux Server
```

Check:

```bash
sudo ss -tulpn | grep nginx
```

Example output:

```text
tcp LISTEN

0.0.0.0:443

nginx
```

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"My website is not accessible."

Check:

```bash
sudo ss -tulpn | grep 443
```

Expected:

```text
LISTEN :443
```

If nothing appears:

Possible causes:

- nginx stopped
- wrong configuration
- service not listening

---

## Problem 2:
"Port is open but application does not work."

Check:

```bash
ss -tulpn
```

Example:

```text
8080 → python application
```

Then test:

```bash
curl localhost:8080
```

---

## Problem 3:
"Which process is using this port?"

Example:

```bash
sudo ss -tulpn | grep 3306
```

Output:

```text
mysqld
```

Now we know:

```text
Port 3306

    |

    v

MySQL Database
```

---

# ☸️ Kubernetes Connection

`ss` is extremely useful on Kubernetes nodes.

Examples:

Check Kubernetes API:

```bash
sudo ss -tulpn | grep 6443
```

Check kubelet:

```bash
sudo ss -tulpn | grep 10250
```

Check container runtime:

```bash
sudo ss -tulpn
```

Networking flow:

```
User

 |

Service Port

 |

Pod Port

 |

Application

```

On the node:

```text
Linux Socket

      |

      v

Container Network

      |

      v

Pod
```

---

# 🆚 ss vs netstat

Older Linux systems used:

```bash
netstat
```

Modern Linux uses:

```bash
ss
```

Comparison:

| Task | Old | Modern |
|-|-|-|
| Connections | netstat | ss |
| Listening ports | netstat -tuln | ss -tuln |
| Processes | netstat -tulpn | ss -tulpn |

`ss` is faster and is the recommended tool.

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `ss -t` | Show TCP connections |
| `ss -u` | Show UDP connections |
| `ss -l` | Show listening sockets |
| `ss -n` | Show numeric addresses |
| `ss -p` | Show processes |
| `ss -tuln` | Show listening TCP/UDP ports |
| `ss -tulpn` | Show ports and processes |
| `ss -a` | Show all sockets |

---

# Conclusion

The `ss` command provides visibility into Linux network communication.

Together with `ip`, it allows administrators to understand:

- Network configuration
- Listening services
- Active connections
- Application communication

For Linux administrators and DevOps engineers, `ss` is one of the fastest ways to diagnose networking problems.