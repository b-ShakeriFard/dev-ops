# 🔥 Linux `firewalld`

> Managing Linux firewall rules dynamically using firewalld and firewall-cmd.

---

# 🎯 What Problem Does It Solve?

`firewalld` provides a simple way to manage network access rules on Linux systems.

It controls:

- Allowed services
- Open ports
- Network zones
- Interface assignments
- Source-based rules

Example:

A web server runs:

```
nginx

TCP 443
```

But users cannot access it.

Possible reason:

```
🔥 Firewall blocks port 443
```

Solution:

```bash
firewall-cmd --add-service=https
```

---

# 🧩 firewalld Architecture

```
Administrator

      |

      v

firewall-cmd

      |

      v

firewalld daemon

      |

      v

nftables

      |

      v

Linux Kernel
```

---

# 🔎 Check firewalld Status

Check service:

```bash
systemctl status firewalld
```

Example:

```
Active: active (running)
```

---

Check firewall state:

```bash
firewall-cmd --state
```

Output:

```
running
```

---

# ▶️ Start and Enable firewalld

Start:

```bash
sudo systemctl start firewalld
```

Enable at boot:

```bash
sudo systemctl enable firewalld
```

Restart:

```bash
sudo systemctl restart firewalld
```

---

# 🛠️ The firewall-cmd Tool

`firewall-cmd` is the command-line interface for firewalld.

General syntax:

```bash
firewall-cmd [options]
```

Examples:

```bash
firewall-cmd --list-all
```

```bash
firewall-cmd --reload
```

---

# 👀 View Current Firewall Rules

Show active zone configuration:

```bash
firewall-cmd --list-all
```

Example:

```
public

services:
 ssh dhcpv6-client

ports:
 none
```

Meaning:

```
SSH allowed

HTTPS blocked
```

---

# 🌐 Show All Zones

Command:

```bash
firewall-cmd --get-zones
```

Example:

```
public
internal
external
trusted
dmz
```

---

# 🔎 Check Active Zone

Command:

```bash
firewall-cmd --get-active-zones
```

Example:

```
public

interfaces:
ens33
```

Meaning:

```
ens33

   |

   v

public zone
```

---

# 🔄 Reload Firewall Rules

After changing permanent rules:

```bash
firewall-cmd --reload
```

Reload:

- Keeps connections alive
- Applies new configuration

---

# ⏱️ Runtime vs Permanent Configuration

One of the most important concepts.

## Runtime Configuration

Temporary:

```bash
firewall-cmd \
--add-port=8080/tcp
```

Check:

```bash
firewall-cmd --list-ports
```

After reboot:

```
Rule disappears
```

---

## Permanent Configuration

Survives reboot:

```bash
firewall-cmd \
--add-port=8080/tcp \
--permanent
```

Apply:

```bash
firewall-cmd --reload
```

---

# 🔁 Copy Runtime Configuration to Permanent

If you tested a rule and want to keep it:

```bash
firewall-cmd --runtime-to-permanent
```

Example:

```
Test rule

    |

    v

Works

    |

    v

Save permanently
```

---

# 🚪 Add a Service

Example:

Allow HTTP:

```bash
firewall-cmd \
--add-service=http
```

Permanent:

```bash
firewall-cmd \
--add-service=http \
--permanent
```

Apply:

```bash
firewall-cmd --reload
```

---

# 🔢 Add a Port

Example:

Allow application port:

```bash
firewall-cmd \
--add-port=8080/tcp
```

Permanent:

```bash
firewall-cmd \
--add-port=8080/tcp \
--permanent
```

---

# ❌ Remove Rules

Remove service:

```bash
firewall-cmd \
--remove-service=http
```

Remove port:

```bash
firewall-cmd \
--remove-port=8080/tcp
```

Permanent removal:

```bash
--permanent
```

---

# 🖥️ Real Rocky Linux Example

Scenario:

A Rocky Linux server runs a web application.

Application:

```
Port:

8080/tcp
```

Check firewall:

```bash
firewall-cmd --list-all
```

Port missing:

```
ports:
none
```

Add:

```bash
firewall-cmd \
--add-port=8080/tcp \
--permanent
```

Reload:

```bash
firewall-cmd --reload
```

Verify:

```bash
firewall-cmd --list-ports
```

Test:

```bash
curl http://server:8080
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
"Service is running but unreachable"

Check service:

```bash
ss -tulpn
```

Example:

```
LISTEN :443
```

Check firewall:

```bash
firewall-cmd --list-all
```

If HTTPS missing:

```bash
firewall-cmd \
--add-service=https \
--permanent
```

---

## Problem 2:
"Rule added but disappeared after reboot"

Cause:

Added runtime rule:

```bash
firewall-cmd --add-port=8080/tcp
```

Solution:

Add permanent:

```bash
firewall-cmd \
--add-port=8080/tcp \
--permanent
```

---

## Problem 3:
"Firewall configuration changed but not active"

Apply:

```bash
firewall-cmd --reload
```

---

# ☸️ Kubernetes Connection

Linux firewalls are important on Kubernetes nodes.

Examples:

Kubernetes API:

```
6443/tcp
```

Kubelet:

```
10250/tcp
```

NodePort:

```
30000-32767/tcp
```

Example:

```
User

 |

NodePort 30080

 |

🔥 firewalld

 |

Kubernetes Service

 |

Pod
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `systemctl status firewalld` | Check service |
| `firewall-cmd --state` | Check firewall state |
| `firewall-cmd --list-all` | Show rules |
| `firewall-cmd --get-zones` | Show zones |
| `firewall-cmd --get-active-zones` | Show active zones |
| `firewall-cmd --reload` | Apply changes |
| `firewall-cmd --add-service` | Allow service |
| `firewall-cmd --add-port` | Allow port |
| `firewall-cmd --remove-port` | Remove port |
| `firewall-cmd --runtime-to-permanent` | Save runtime rules |

---

# Conclusion

`firewalld` provides a flexible and dynamic way to manage Linux firewall rules.

Understanding:

- `firewall-cmd`
- Runtime vs Permanent rules
- Zones
- Services
- Ports

is essential for securing Linux servers and managing enterprise infrastructure.