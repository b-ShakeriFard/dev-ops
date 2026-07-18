# 🧩 firewalld Services

> Managing predefined firewall rules using service names instead of raw port numbers.

---

# 🎯 What Problem Do Services Solve?

A firewalld service is a predefined collection of:

- Ports
- Protocols
- Helper modules
- Connection-tracking settings

Instead of remembering:

```text
HTTPS → TCP 443
```

you can use:

```bash
firewall-cmd --add-service=https
```

This makes firewall rules easier to read and manage.

---

# 🔎 List Available Services

```bash
firewall-cmd --get-services
```

Example:

```text
cockpit dhcp dns http https ldap ldaps mysql postgresql ssh
```

---

# 👀 List Allowed Services

Show services in the default zone:

```bash
firewall-cmd --list-services
```

Example:

```text
ssh dhcpv6-client
```

Show services in a specific zone:

```bash
firewall-cmd \
--zone=public \
--list-services
```

---

# ➕ Allow a Service

Allow HTTP temporarily:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=http
```

Allow HTTP permanently:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=http \
--permanent
```

Apply:

```bash
sudo firewall-cmd --reload
```

---

# ➖ Remove a Service

Remove HTTP temporarily:

```bash
sudo firewall-cmd \
--zone=public \
--remove-service=http
```

Remove it permanently:

```bash
sudo firewall-cmd \
--zone=public \
--remove-service=http \
--permanent
```

Apply:

```bash
sudo firewall-cmd --reload
```

---

# ✅ Check Whether a Service Is Allowed

```bash
firewall-cmd \
--zone=public \
--query-service=https
```

Possible output:

```text
yes
```

or:

```text
no
```

---

# 🌐 Common Firewall Services

| Service | Typical Port |
|---|---:|
| `ssh` | 22/tcp |
| `http` | 80/tcp |
| `https` | 443/tcp |
| `dns` | 53/tcp and 53/udp |
| `ldap` | 389/tcp and udp |
| `ldaps` | 636/tcp |
| `mysql` | 3306/tcp |
| `postgresql` | 5432/tcp |
| `cockpit` | 9090/tcp |

---

# 🖥️ Real Linux Example

Scenario:

A Rocky Linux server runs nginx over HTTPS.

Check the service:

```bash
sudo ss -tulpn | grep 443
```

Allow HTTPS:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=https \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd \
--zone=public \
--query-service=https
```

Test:

```bash
curl -I https://server.example.com
```

---

# 🏢 Internal Service Example

Suppose LDAP should only be reachable from the internal network.

Allow LDAP in the internal zone:

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=ldap \
--permanent
```

Allow LDAPS:

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=ldaps \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

---

# 🔍 Inspect a Service Definition

Show details:

```bash
firewall-cmd \
--info-service=https
```

Example output:

```text
https
  ports: 443/tcp
```

Inspect SSH:

```bash
firewall-cmd \
--info-service=ssh
```

---

# 📁 Service Definition Files

Custom and system service definitions are stored under:

```text
/usr/lib/firewalld/services/
```

and:

```text
/etc/firewalld/services/
```

System-provided definitions:

```text
/usr/lib/firewalld/services/
```

Custom administrator definitions:

```text
/etc/firewalld/services/
```

---

# 🛠️ Create a Custom Service

Suppose an application uses:

```text
TCP 8080
```

Create a service:

```bash
sudo firewall-cmd \
--permanent \
--new-service=myapp
```

Add a description:

```bash
sudo firewall-cmd \
--permanent \
--service=myapp \
--set-description="Internal web application"
```

Add the port:

```bash
sudo firewall-cmd \
--permanent \
--service=myapp \
--add-port=8080/tcp
```

Allow the service:

```bash
sudo firewall-cmd \
--zone=internal \
--add-service=myapp \
--permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd \
--info-service=myapp
```

---

# 🔄 Service vs Port Rule

Using a service:

```bash
firewall-cmd --add-service=https
```

Using a raw port:

```bash
firewall-cmd --add-port=443/tcp
```

Both may allow the same traffic, but services are usually clearer.

```text
https

   |

   v

TCP 443
```

Use service names when a predefined service exists.

Use raw ports for custom applications.

---

# 🚨 Troubleshooting Scenarios

## Service Is Running but Unreachable

Check listening port:

```bash
sudo ss -tulpn
```

Check allowed services:

```bash
firewall-cmd \
--zone=public \
--list-services
```

Add the missing service:

```bash
sudo firewall-cmd \
--zone=public \
--add-service=https \
--permanent
```

---

## Service Added to the Wrong Zone

Check active zones:

```bash
firewall-cmd --get-active-zones
```

Check the correct zone:

```bash
firewall-cmd \
--zone=internal \
--list-services
```

Add the service to the zone assigned to the interface.

---

## Rule Disappeared After Reboot

The rule was added only at runtime.

Compare:

```bash
firewall-cmd --list-services
```

with:

```bash
firewall-cmd \
--list-services \
--permanent
```

Add permanently and reload.

---

# ☸️ DevOps Examples

## Harbor

```text
https → 443/tcp
```

```bash
sudo firewall-cmd \
--add-service=https \
--permanent
```

## LDAP

```text
ldap  → 389
ldaps → 636
```

## Web Application

```text
http  → 80
https → 443
```

For Kubernetes-specific ports such as `6443` or NodePorts, raw port rules are often used instead of predefined services.

---

# 📚 Quick Reference

| Command | Purpose |
|---|---|
| `firewall-cmd --get-services` | List available services |
| `firewall-cmd --list-services` | List allowed services |
| `firewall-cmd --add-service=NAME` | Allow a service |
| `firewall-cmd --remove-service=NAME` | Remove a service |
| `firewall-cmd --query-service=NAME` | Check a service |
| `firewall-cmd --info-service=NAME` | Inspect a definition |
| `firewall-cmd --new-service=NAME --permanent` | Create custom service |
| `firewall-cmd --service=NAME --add-port=PORT/PROTO --permanent` | Add port to custom service |

---

# Conclusion

firewalld services provide readable, reusable firewall rules.

Instead of managing only port numbers, administrators can work with meaningful names such as:

```text
ssh
http
https
ldap
postgresql
```

This makes Linux firewall configuration easier to understand, maintain, and troubleshoot.