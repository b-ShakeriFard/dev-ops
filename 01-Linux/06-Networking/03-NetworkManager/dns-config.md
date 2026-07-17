# 🌐 Configure DNS with NetworkManager

> Managing Linux DNS configuration using NetworkManager and `nmcli`.

---

# 🎯 What Problem Does It Solve?

DNS allows Linux systems to translate names into IP addresses.

Example:

```
harbor.example.com

        |

        v

192.168.252.222
```

Without DNS:

```bash
curl https://192.168.252.222
```

With DNS:

```bash
curl https://harbor.example.com
```

DNS configuration is required for:

- Server communication
- Web applications
- LDAP
- Kubernetes
- OpenShift
- Cloud environments

---

# 🧩 DNS Configuration Components

A Linux DNS configuration contains:

```
DNS Server

      +

Search Domain

      +

Resolver Configuration
```

Example:

```
DNS Server:

192.168.1.10


Search Domain:

example.com
```

---

# 🔎 View Current DNS Settings

## Check resolver file

```bash
cat /etc/resolv.conf
```

Example:

```
nameserver 192.168.1.10
nameserver 8.8.8.8
```

---

## Check NetworkManager DNS

```bash
nmcli device show
```

Example:

```
IP4.DNS:

192.168.1.10
```

---

# 📋 View Connection DNS Settings

Show connections:

```bash
nmcli connection show
```

Example:

```
NAME

Production-LAN
```

Show details:

```bash
nmcli connection show Production-LAN
```

Look for:

```
ipv4.dns
```

---

# 🌐 Configure DNS Server

Example:

Set DNS server:

```bash
sudo nmcli connection modify Production-LAN \
ipv4.dns 192.168.1.10
```

Add multiple DNS servers:

```bash
sudo nmcli connection modify Production-LAN \
ipv4.dns "192.168.1.10 8.8.8.8"
```

---

# 🔄 Apply DNS Configuration

Restart connection:

```bash
nmcli connection down Production-LAN

nmcli connection up Production-LAN
```

or:

```bash
nmcli connection reload
```

---

# 🏷️ Configure Search Domain

A search domain allows shorter names.

Example:

Configured:

```
example.com
```

Now:

```bash
ping server01
```

becomes:

```bash
ping server01.example.com
```

Configure:

```bash
nmcli connection modify Production-LAN \
ipv4.dns-search example.com
```

---

# 🚫 Ignore DHCP DNS

When using static DNS, prevent DHCP from adding DNS servers:

```bash
nmcli connection modify Production-LAN \
ipv4.ignore-auto-dns yes
```

This is common for servers.

---

# ✅ Verify DNS Configuration

Check:

```bash
cat /etc/resolv.conf
```

Expected:

```
nameserver 192.168.1.10
```

---

Test resolution:

```bash
dig harbor.example.com
```

Expected:

```
ANSWER SECTION:

harbor.example.com

192.168.252.222
```

---

# 🖥️ Real Rocky Linux Example

Scenario:

A Harbor server needs internal DNS.

Requirements:

```
Hostname:

harbor.company.local


IP:

192.168.252.222


DNS Server:

192.168.252.10
```

Configure:

```bash
nmcli con mod ens192 \
ipv4.dns 192.168.252.10

nmcli con mod ens192 \
ipv4.dns-search company.local
```

Apply:

```bash
nmcli con up ens192
```

Verify:

```bash
dig harbor.company.local
```

---

# 🛠️ Troubleshooting Scenarios

## Problem 1:
"IP works but hostname fails"

Test:

```bash
ping 8.8.8.8
```

Works.

Test:

```bash
ping google.com
```

Fails.

Check:

```bash
cat /etc/resolv.conf
```

Possible issue:

- Wrong DNS server
- DNS service unavailable

---

## Problem 2:
"Wrong DNS server is being used"

Check:

```bash
nmcli device show
```

Find:

```
IP4.DNS
```

Fix:

```bash
nmcli con mod NAME \
ipv4.dns NEW_DNS
```

---

## Problem 3:
"Configuration disappears after reboot"

Cause:

Manual changes using:

```bash
ip addr
```

or:

```bash
/etc/resolv.conf
```

were temporary.

Solution:

Configure through:

```bash
nmcli
```

---

# ☸️ Kubernetes Connection

DNS is critical in Kubernetes.

Inside a Pod:

```
Application

      |

      v

CoreDNS

      |

      v

Service IP
```

Example:

```bash
curl postgres.database.svc.cluster.local
```

Kubernetes resolves:

```
postgres.database.svc.cluster.local

              |

              v

Service IP
```

---

# 🏢 Enterprise DNS Example

A typical environment:

```
DNS Server

    |

    +-- harbor.company.local

    +-- ldap.company.local

    +-- worker01.company.local

    +-- api.cluster.company.local

```

Correct DNS is required before deploying:

- Kubernetes
- OpenShift
- LDAP
- Harbor

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `nmcli device show` | View DNS settings |
| `nmcli connection show` | View profiles |
| `nmcli con mod ipv4.dns` | Configure DNS |
| `nmcli con mod ipv4.dns-search` | Configure search domain |
| `cat /etc/resolv.conf` | Check resolver |
| `dig hostname` | Test DNS |
| `nslookup hostname` | DNS lookup |

---

# Conclusion

DNS configuration is a fundamental part of Linux networking.

Using NetworkManager, administrators can manage DNS settings consistently and reliably across modern Linux systems.

Correct DNS configuration is essential for enterprise services, containers, Kubernetes, and cloud infrastructure.