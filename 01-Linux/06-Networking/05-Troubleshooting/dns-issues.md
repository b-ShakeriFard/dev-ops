# 🌍 DNS Troubleshooting

> Diagnosing Linux name resolution problems.

---

# 🎯 What Problem Does It Solve?

DNS troubleshooting answers:

> "Why can I reach an IP address, but not a hostname?"

Example:

Works:

```bash
ping 8.8.8.8
```

Fails:

```bash
ping google.com
```

Possible problem:

```
DNS resolution
```

---

# 🧩 DNS Troubleshooting Flow

```text
Application

      |

      v

Hostname

      |

      v

DNS Resolver

      |

      v

DNS Server

      |

      v

IP Address

      |

      v

Network Connection
```

---

# 1️⃣ Check Current DNS Configuration

View resolver configuration:

```bash
cat /etc/resolv.conf
```

Example:

```text
nameserver 192.168.1.10
nameserver 8.8.8.8
```

Meaning:

```
Linux System

      |

      v

DNS Server
```

---

# 2️⃣ Check NetworkManager DNS Settings

View device information:

```bash
nmcli device show
```

Look for:

```text
IP4.DNS
```

Example:

```text
IP4.DNS:

192.168.1.10
```

---

# 3️⃣ Test DNS Resolution

Use:

```bash
dig google.com
```

Example:

```text
ANSWER SECTION:

google.com

142.250.x.x
```

Successful:

```
Hostname → IP
```

---

# 4️⃣ Use dig for Troubleshooting

## Basic Query

```bash
dig example.com
```

---

## Short Output

```bash
dig +short example.com
```

Example:

```text
93.184.216.34
```

---

## Query Specific DNS Server

Test Google DNS:

```bash
dig @8.8.8.8 example.com
```

Test internal DNS:

```bash
dig @192.168.1.10 internal.company.local
```

---

# 5️⃣ Check DNS Server Reachability

DNS normally uses:

```
UDP 53
```

and sometimes:

```
TCP 53
```

Test:

```bash
nc -zv dns-server 53
```

Example:

```bash
nc -zv 192.168.1.10 53
```

---

# 6️⃣ Check Local Hosts File

Before DNS lookup, Linux checks:

```bash
/etc/hosts
```

Example:

```text
192.168.1.50 server01
```

Test:

```bash
cat /etc/hosts
```

A wrong entry can override DNS.

---

# 7️⃣ Check Hostname Configuration

Show hostname:

```bash
hostname
```

Show full information:

```bash
hostnamectl
```

Example:

```text
Static hostname:

worker01.company.local
```

---

# 🖥️ Real Linux Scenario

Problem:

```
curl https://harbor.company.local
```

fails.

---

## Step 1: Check IP connectivity

```bash
ping 192.168.252.222
```

Works.

Meaning:

```
Network is OK
```

---

## Step 2: Test DNS

```bash
dig harbor.company.local
```

Result:

```
SERVFAIL
```

Problem:

```
DNS server issue
```

---

## Step 3: Check DNS configuration

```bash
cat /etc/resolv.conf
```

Wrong:

```text
nameserver 10.0.0.1
```

Correct:

```text
nameserver 192.168.252.10
```

---

## Step 4: Fix with NetworkManager

```bash
nmcli connection show
```

Modify:

```bash
nmcli con mod ens192 \
ipv4.dns 192.168.252.10
```

Apply:

```bash
nmcli con up ens192
```

Test:

```bash
dig harbor.company.local
```

---

# 🚨 Common DNS Problems

---

## Problem 1:
## IP Works, Hostname Fails

Example:

```bash
ping 8.8.8.8
```

works.

```bash
ping google.com
```

fails.

Check:

```bash
cat /etc/resolv.conf
```

---

## Problem 2:
## Wrong DNS Server

Check:

```bash
nmcli device show
```

Fix:

```bash
nmcli con mod NAME \
ipv4.dns DNS_SERVER
```

---

## Problem 3:
## DNS Record Missing

Query:

```bash
dig server.company.local
```

Output:

```text
NXDOMAIN
```

Meaning:

```
Record does not exist
```

Solution:

Create the DNS record.

---

## Problem 4:
## DNS Works Internally but Not Externally

Example:

Works:

```bash
dig ldap.company.local
```

Fails:

```bash
dig google.com
```

Possible causes:

- Internal DNS has no forwarders
- Firewall blocks DNS traffic
- DNS server unavailable

---

# 🏢 Enterprise DNS Example

Typical infrastructure:

```text
Internal DNS Server

        |

        +-- harbor.company.local

        +-- ldap.company.local

        +-- nexus.company.local

        +-- worker01.company.local
```

Every infrastructure component depends on correct DNS.

---

# ☸️ Kubernetes Connection

Kubernetes has its own DNS system:

```text
Application Pod

      |

      v

CoreDNS

      |

      v

Service Discovery
```

Example:

```bash
curl postgres.database.svc.cluster.local
```

Resolution:

```
postgres.database.svc.cluster.local

          |

          v

Service IP

          |

          v

Pod Endpoint
```

Troubleshoot:

Check CoreDNS:

```bash
kubectl get pods -n kube-system
```

Check DNS from a pod:

```bash
kubectl exec -it pod-name -- nslookup kubernetes.default
```

---

# 📚 Quick Reference

| Command | Purpose |
|---|---|
| `cat /etc/resolv.conf` | Check resolver |
| `nmcli device show` | View DNS settings |
| `dig hostname` | Test DNS |
| `dig +short hostname` | Simple lookup |
| `dig @server hostname` | Query specific DNS server |
| `cat /etc/hosts` | Check local mappings |
| `hostnamectl` | Check hostname |
| `nc -zv SERVER 53` | Test DNS port |

---

# Conclusion

DNS troubleshooting should happen after basic connectivity is confirmed.

The correct order is:

```text
Network

   ↓

IP Connectivity

   ↓

DNS Configuration

   ↓

DNS Server

   ↓

Hostname Resolution

   ↓

Application
```

A Linux administrator should always separate:

```
"Can I reach the machine?"

from:

"Can I find the machine?"
```

DNS is the bridge between human-friendly names and network communication.