# 🌍 Linux DNS (Domain Name System)

> Translating names into IP addresses.

---

# 🔎 What is DNS?

DNS allows humans to use names instead of remembering IP addresses.

Example:

```
harbor.mylocalsetup.com

          |

          v

192.168.252.222
```

Instead of:

```bash
curl https://192.168.252.222
```

we use:

```bash
curl https://harbor.sarvrayaneh.com
```

---

# 🧩 DNS Resolution Process

When Linux needs to resolve a hostname:

```
Application

    |

    v

Resolver

    |

    v

DNS Server

    |

    v

IP Address
```

Example:

```
ping google.com

        |

        v

DNS lookup

        |

        v

142.x.x.x
```

---

# 📄 Local Name Resolution

Before asking a DNS server, Linux checks:

```text
/etc/hosts
```

Example:

```bash
cat /etc/hosts
```

Output:

```
192.168.1.50   server01
192.168.1.60   database
```

Now:

```bash
ping server01
```

does not need DNS.

---

# ⚙️ DNS Configuration

Linux DNS configuration:

```text
/etc/resolv.conf
```

Example:

```bash
cat /etc/resolv.conf
```

Output:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
```

Meaning:

```
Ask these DNS servers
when resolving names
```

---

# 🔎 DNS Query with dig

The most useful DNS troubleshooting tool:

```bash
dig example.com
```

Example:

```
ANSWER SECTION:

example.com

93.184.216.34
```

---

# 📚 DNS Record Types

## A Record

Hostname → IPv4

Example:

```
server.example.com

        |

        v

192.168.1.10
```

Query:

```bash
dig A server.example.com
```

---

## AAAA Record

Hostname → IPv6

Example:

```
server.example.com

        |

        v

2001:db8::10
```

---

## CNAME Record

Alias to another hostname.

Example:

```
www.example.com

        |

        v

example.com
```

---

## MX Record

Mail servers.

Example:

```bash
dig MX example.com
```

---

# 🛠️ DNS Troubleshooting Commands

## Check DNS resolution

```bash
dig google.com
```

---

## Simple lookup

```bash
nslookup google.com
```

---

## Check hostname

```bash
hostname
```

---

## Check local resolver

```bash
cat /etc/resolv.conf
```

---

# 🔐 DNS and Enterprise Linux

Enterprise environments commonly use internal DNS.

Examples:

```
server01.company.local

ldap.company.local

harbor.company.local
```

DNS provides:

- Server discovery
- Service names
- Authentication integration
- Application connectivity

---

# ☸️ DNS in Kubernetes

Kubernetes provides internal DNS using:

```
CoreDNS
```

Example:

A Service:

```
postgres-service

        |

        v

10.96.0.10
```

Inside a Pod:

```bash
curl postgres-service
```

works because Kubernetes DNS resolves:

```
postgres-service

        |

        v

Service IP
```

---

# 🏢 Real World Example

A DevOps environment:

```
User

 |

harbor.company.com

 |

DNS

 |

192.168.252.222

 |

Harbor Service

```

---

# 🛠️ DNS Troubleshooting Flow

```
1. Check hostname

        |
        v

hostname


2. Check resolver

        |
        v

cat /etc/resolv.conf


3. Query DNS

        |
        v

dig hostname


4. Test connectivity

        |
        v

ping / curl

```

---

# Conclusion

DNS is a critical service that connects human-friendly names with machine addresses.

Linux administrators use DNS every day for server management, application communication, authentication, and modern infrastructure platforms such as Kubernetes.

```