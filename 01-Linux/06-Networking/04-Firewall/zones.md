# 🌍 firewalld Zones

> Applying different firewall policies to different networks and interfaces.

---

# 🎯 What Problem Do Zones Solve?

Not every network should receive the same level of trust.

For example:

```text
Home Network     → More trusted
Public Wi-Fi     → Less trusted
Internet-facing  → Strictly controlled
```

A firewalld **zone** is a collection of firewall rules representing a particular trust level.

---

# 🧩 Basic Concept

```text
Network Interface

        |

        v

firewalld Zone

        |

        v

Allowed Services and Ports
```

Example:

```text
ens33

   |

   v

public zone

   |

   +-- SSH allowed
   +-- HTTPS allowed
   +-- Other traffic blocked
```

---

# 🔎 List Available Zones

```bash
firewall-cmd --get-zones
```

Example:

```text
block dmz drop external home internal public trusted work
```

---

# 🌐 Common Zones

| Zone | Typical Usage |
|---|---|
| `drop` | Silently drop unsolicited incoming traffic |
| `block` | Reject unsolicited incoming traffic |
| `public` | Public or untrusted networks |
| `external` | External networks with masquerading |
| `dmz` | Public-facing servers with limited access |
| `work` | Workplace networks |
| `home` | Home networks |
| `internal` | Trusted internal networks |
| `trusted` | Accept almost all network connections |

---

# ⭐ Default Zone

Show the default zone:

```bash
firewall-cmd --get-default-zone
```

Example:

```text
public
```

Set a new default zone:

```bash
sudo firewall-cmd --set-default-zone=internal
```

The default zone is used when an interface or source has no explicit zone assignment.

---

# 🔌 Show Active Zones

```bash
firewall-cmd --get-active-zones
```

Example:

```text
public
  interfaces: ens33

internal
  interfaces: ens34
```

Meaning:

```text
ens33 → public rules

ens34 → internal rules
```

---

# 👀 Inspect a Zone

Show one zone:

```bash
firewall-cmd --zone=public --list-all
```

Example:

```text
public
  interfaces: ens33
  services: ssh https
  ports: 8080/tcp
```

Show all zones:

```bash
firewall-cmd --list-all-zones
```

---

# 🔄 Assign an Interface to a Zone

Runtime assignment:

```bash
sudo firewall-cmd \
--zone=internal \
--change-interface=ens34
```

Permanent assignment:

```bash
sudo firewall-cmd \
--zone=internal \
--change-interface=ens34 \
--permanent
```

Apply permanent changes:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd --get-active-zones
```

---

# ➕ Add an Interface to a Zone

```bash
sudo firewall-cmd \
--zone=public \
--add-interface=ens33 \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

---

# ➖ Remove an Interface from a Zone

```bash
sudo firewall-cmd \
--zone=public \
--remove-interface=ens33 \
--permanent
```

---

# 🚪 Add Services to a Specific Zone

Allow HTTPS only in the public zone:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=https \
--permanent
```

Allow LDAP only in the internal zone:

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=ldap \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

---

# 🔢 Add a Port to a Specific Zone

Allow TCP port 8080 in the public zone:

```bash
sudo firewall-cmd \
--zone=public \
--add-port=8080/tcp \
--permanent
```

Verify:

```bash
firewall-cmd \
--zone=public \
--list-ports
```

---

# 🖥️ Real Linux Example

A server has two interfaces:

```text
ens33 → Internet-facing network

ens34 → Internal management network
```

Desired configuration:

```text
ens33 → public zone
        HTTPS allowed

ens34 → internal zone
        SSH and LDAP allowed
```

Commands:

```bash
sudo firewall-cmd \
--zone=public \
--change-interface=ens33 \
--permanent
```

```bash
sudo firewall-cmd \
--zone=internal \
--change-interface=ens34 \
--permanent
```

```bash
sudo firewall-cmd \
--zone=public \
--add-service=https \
--permanent
```

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=ssh \
--permanent
```

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=ldap \
--permanent
```

Apply:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd --get-active-zones
```

---

# 📍 Source-Based Zone Assignment

Zones can also apply to source networks.

Example:

Trust an internal subnet:

```bash
sudo firewall-cmd \
--zone=internal \
--add-source=192.168.10.0/24 \
--permanent
```

Remove it:

```bash
sudo firewall-cmd \
--zone=internal \
--remove-source=192.168.10.0/24 \
--permanent
```

This is useful when traffic arrives through the same interface but comes from different networks.

---

# 🚨 Troubleshooting Scenarios

## Service Added to the Wrong Zone

The service is allowed:

```bash
firewall-cmd --zone=internal --list-services
```

But the interface belongs to:

```text
public
```

Check:

```bash
firewall-cmd --get-active-zones
```

Add the service to the active zone:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=https \
--permanent
```

---

## Interface Has No Expected Zone

Check:

```bash
firewall-cmd --get-zone-of-interface=ens33
```

Assign it:

```bash
sudo firewall-cmd \
--zone=public \
--change-interface=ens33 \
--permanent
```

---

## Runtime and Permanent Rules Differ

Runtime view:

```bash
firewall-cmd --zone=public --list-all
```

Permanent view:

```bash
firewall-cmd \
--zone=public \
--list-all \
--permanent
```

Apply permanent configuration:

```bash
sudo firewall-cmd --reload
```

---

# ☸️ DevOps Example

A Kubernetes node may have:

```text
Management Interface

        |

        v

internal zone

        |

        +-- SSH
        +-- kubelet
        +-- Kubernetes API
```

A public-facing interface may use:

```text
public zone

        |

        +-- HTTPS
        +-- Selected NodePorts
```

Zones help separate trusted cluster traffic from external traffic.

---

# 📚 Quick Reference

| Command | Purpose |
|---|---|
| `firewall-cmd --get-zones` | List available zones |
| `firewall-cmd --get-default-zone` | Show default zone |
| `firewall-cmd --set-default-zone=ZONE` | Change default zone |
| `firewall-cmd --get-active-zones` | Show active zones |
| `firewall-cmd --zone=ZONE --list-all` | Inspect a zone |
| `firewall-cmd --get-zone-of-interface=IFACE` | Find interface zone |
| `firewall-cmd --zone=ZONE --change-interface=IFACE` | Move interface to zone |
| `firewall-cmd --zone=ZONE --add-service=SERVICE` | Allow service in zone |
| `firewall-cmd --zone=ZONE --add-port=PORT/PROTO` | Allow port in zone |
| `firewall-cmd --zone=ZONE --add-source=NETWORK` | Assign source network |

---

# Conclusion

firewalld zones let administrators apply different security policies to different interfaces and networks.

The essential relationship is:

```text
Interface or Source

        |

        v

Zone

        |

        v

Allowed Services and Ports
```

Understanding zones makes firewall configuration safer, clearer, and easier to manage.