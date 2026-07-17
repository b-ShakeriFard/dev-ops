# 🔎 Linux `dig` Command

> Querying DNS servers and troubleshooting name resolution.

---

# 🎯 What Problem Does It Solve?

The `dig` command helps administrators answer:

> "Can this hostname be translated into an IP address?"

DNS problems are common causes of:

- Website failures
- Application connection problems
- Kubernetes service discovery issues
- LDAP authentication problems

Examples:

```bash
dig google.com
```

or:

```bash
dig harbor.example.com
```

---

# 🧩 Basic Syntax

General syntax:

```bash
dig [options] hostname
```

Example:

```bash
dig example.com
```

---

# 📄 Basic DNS Query

Command:

```bash
dig example.com
```

Example output:

```text
;; ANSWER SECTION:

example.com.

93.184.216.34
```

Meaning:

```text
Hostname

    |

    v

IP Address
```

---

# 🌐 Check A Record

An A record maps:

```text
Hostname → IPv4 Address
```

Example:

```bash
dig A example.com
```

Output:

```text
example.com

93.184.216.34
```

---

# 🌍 Check AAAA Record

An AAAA record maps:

```text
Hostname → IPv6 Address
```

Command:

```bash
dig AAAA example.com
```

Example:

```text
2001:db8::10
```

---

# 🔗 Check CNAME Record

A CNAME creates an alias.

Example:

```bash
dig CNAME www.example.com
```

Output:

```text
www.example.com

CNAME

example.com
```

Meaning:

```text
www.example.com

        |

        v

example.com
```

---

# 📮 Check MX Record

MX records define mail servers.

Command:

```bash
dig MX example.com
```

Example:

```text
mail.example.com
```

---

# 🖥️ Query a Specific DNS Server

By default, Linux uses configured DNS servers.

Check:

```bash
cat /etc/resolv.conf
```

Example:

```text
nameserver 8.8.8.8
```

Query a specific DNS server:

```bash
dig @8.8.8.8 example.com
```

Useful when troubleshooting DNS differences.

---

# 🔍 Short Output

Normal output can be long.

Use:

```bash
dig +short example.com
```

Example:

```text
93.184.216.34
```

Useful in scripts.

---

# 🔄 Reverse DNS Lookup

Find hostname from an IP address.

Command:

```bash
dig -x 8.8.8.8
```

Example:

```text
dns.google
```

Meaning:

```text
IP Address

    |

    v

Hostname
```

---

# 🛠️ Real Linux Examples

## Example 1: Check Server DNS

Problem:

```
Cannot connect to harbor.example.com
```

Check:

```bash
dig harbor.example.com
```

Expected:

```text
harbor.example.com

192.168.252.222
```

If no answer:

Possible DNS problem.

---

## Example 2: Compare DNS Servers

Query local DNS:

```bash
dig example.com
```

Query Google DNS:

```bash
dig @8.8.8.8 example.com
```

Compare results.

---

## Example 3: Check Kubernetes DNS

Inside a Pod:

```bash
kubectl exec -it pod-name -- sh
```

Then:

```bash
dig kubernetes.default
```

Expected:

```text
kubernetes.default.svc.cluster.local

10.96.0.1
```

---

# 🐧 DNS Files in Linux

Important files:

## Resolver Configuration

```bash
/etc/resolv.conf
```

Contains:

- DNS servers
- Search domains

---

## Local DNS Entries

```bash
/etc/hosts
```

Example:

```text
192.168.1.50 server01
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
"IP works, hostname fails"

Test:

Works:

```bash
ping 8.8.8.8
```

Fails:

```bash
ping google.com
```

Check:

```bash
dig google.com
```

Possible issue:

- DNS server unavailable
- Wrong resolver configuration

---

## Problem 2:
"Different machines resolve different IPs"

Compare:

```bash
dig hostname
```

and:

```bash
dig @8.8.8.8 hostname
```

Possible issue:

- DNS propagation
- Internal DNS problem
- Wrong DNS server

---

## Problem 3:
"Kubernetes service name does not resolve"

Inside Pod:

```bash
dig service-name.namespace.svc.cluster.local
```

Check:

```bash
kubectl get pods -n kube-system
```

Look for:

```text
coredns
```

---

# ☸️ Kubernetes Connection

Kubernetes uses DNS heavily.

Example Service:

```yaml
metadata:
  name: database
```

Kubernetes creates:

```text
database.namespace.svc.cluster.local
```

Traffic flow:

```
Application

    |

    v

DNS Query

    |

    v

CoreDNS

    |

    v

Service IP

    |

    v

Pod
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `dig hostname` | Basic DNS query |
| `dig +short hostname` | Show only answer |
| `dig A hostname` | IPv4 lookup |
| `dig AAAA hostname` | IPv6 lookup |
| `dig CNAME hostname` | Alias lookup |
| `dig MX hostname` | Mail server lookup |
| `dig -x IP` | Reverse lookup |
| `dig @server hostname` | Query specific DNS server |

---

# Conclusion

The `dig` command provides visibility into DNS resolution.

Together with:

```text
ip → ping → ss → dig → curl
```

it allows Linux administrators to troubleshoot network communication from connectivity all the way to application access.

DNS knowledge is essential for managing Linux servers, enterprise infrastructure, and Kubernetes environments.