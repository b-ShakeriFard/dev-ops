# 🚪 firewalld Ports

> Opening, closing, and verifying custom network ports with `firewall-cmd`.

---

# 🎯 What Problem Do Port Rules Solve?

Not every application has a predefined firewalld service.

Custom applications may use ports such as:

```text
8080/tcp
5000/tcp
9090/tcp
30000-32767/tcp
```

In these cases, administrators can allow the required port directly.

---

# 🧩 Basic Syntax

Allow a port:

```bash
firewall-cmd --add-port=PORT/PROTOCOL
```

Example:

```bash
sudo firewall-cmd --add-port=8080/tcp
```

---

# ➕ Open a Port

Open TCP port `8080` temporarily:

```bash
sudo firewall-cmd \
--zone=public \
--add-port=8080/tcp
```

Verify:

```bash
firewall-cmd \
--zone=public \
--list-ports
```

---

# 💾 Open a Port Permanently

```bash
sudo firewall-cmd \
--zone=public \
--add-port=8080/tcp \
--permanent
```

Apply:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd \
--zone=public \
--list-ports
```

---

# ➖ Close a Port

Remove the runtime rule:

```bash
sudo firewall-cmd \
--zone=public \
--remove-port=8080/tcp
```

Remove the permanent rule:

```bash
sudo firewall-cmd \
--zone=public \
--remove-port=8080/tcp \
--permanent
```

Apply:

```bash
sudo firewall-cmd --reload
```

---

# ✅ Check Whether a Port Is Open

```bash
firewall-cmd \
--zone=public \
--query-port=8080/tcp
```

Possible results:

```text
yes
```

or:

```text
no
```

---

# 🚚 TCP vs UDP

A port rule must include the protocol.

TCP example:

```bash
sudo firewall-cmd \
--add-port=443/tcp
```

UDP example:

```bash
sudo firewall-cmd \
--add-port=53/udp
```

Remember:

```text
53/tcp ≠ 53/udp
```

They are separate firewall rules.

---

# 🔢 Open a Port Range

Example:

```bash
sudo firewall-cmd \
--zone=public \
--add-port=30000-32767/tcp \
--permanent
```

This is commonly associated with Kubernetes NodePort Services.

Apply:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
firewall-cmd \
--zone=public \
--list-ports
```

---

# 🌍 Open a Port in a Specific Zone

Public-facing application:

```bash
sudo firewall-cmd \
--zone=public \
--add-port=443/tcp \
--permanent
```

Internal database:

```bash
sudo firewall-cmd \
--zone=internal \
--add-port=5432/tcp \
--permanent
```

This allows different access policies for different networks.

---

# 🖥️ Real Linux Example

Scenario:

A Python application listens on:

```text
TCP 5000
```

Check the application:

```bash
sudo ss -tulpn | grep 5000
```

Expected:

```text
LISTEN 0.0.0.0:5000
```

Open the port:

```bash
sudo firewall-cmd \
--zone=public \
--add-port=5000/tcp \
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
--query-port=5000/tcp
```

Test remotely:

```bash
curl http://server-ip:5000
```

---

# 🛠️ Testing an Open Port

From another machine:

```bash
nc -zv server-ip 8080
```

Example:

```bash
nc -zv 192.168.1.50 8080
```

Successful result:

```text
Connection succeeded
```

For HTTP services:

```bash
curl http://192.168.1.50:8080
```

---

# 🔍 Service Listening vs Firewall Open

These are two separate requirements:

```text
Application listening
        +
Firewall allowing
        =
Remote access
```

Check listening process:

```bash
sudo ss -tulpn
```

Check firewall:

```bash
firewall-cmd --list-ports
```

A port can be allowed by the firewall while no application is listening.

A service can also be listening while the firewall blocks remote access.

---

# 🏢 Real DevOps Examples

## Harbor Registry

Custom registry port:

```text
5000/tcp
```

```bash
sudo firewall-cmd \
--add-port=5000/tcp \
--permanent
```

---

## Prometheus

```text
9090/tcp
```

```bash
sudo firewall-cmd \
--add-port=9090/tcp \
--permanent
```

---

## Grafana

```text
3000/tcp
```

```bash
sudo firewall-cmd \
--add-port=3000/tcp \
--permanent
```

---

## Kubernetes API

```text
6443/tcp
```

```bash
sudo firewall-cmd \
--add-port=6443/tcp \
--permanent
```

---

## Kubernetes NodePort

Default range:

```text
30000-32767/tcp
```

```bash
sudo firewall-cmd \
--add-port=30000-32767/tcp \
--permanent
```

Only open the range when required by your design.

---

# 🚨 Troubleshooting Scenarios

## Port Is Open but Service Is Unreachable

Check:

```bash
sudo ss -tulpn | grep 8080
```

If nothing appears:

```text
The firewall is open,
but no application is listening.
```

---

## Service Is Listening but Remote Access Fails

Check firewall:

```bash
firewall-cmd \
--zone=public \
--query-port=8080/tcp
```

Also verify the active zone:

```bash
firewall-cmd --get-active-zones
```

---

## Rule Disappears After Reboot

The port was opened only at runtime.

Compare:

```bash
firewall-cmd --list-ports
```

with:

```bash
firewall-cmd \
--list-ports \
--permanent
```

Add the rule permanently and reload.

---

## Port Was Added to the Wrong Zone

Check the interface zone:

```bash
firewall-cmd \
--get-zone-of-interface=ens33
```

Then inspect that zone:

```bash
firewall-cmd \
--zone=public \
--list-ports
```

---

# 📚 Quick Reference

| Command | Purpose |
|---|---|
| `firewall-cmd --add-port=8080/tcp` | Open runtime port |
| `firewall-cmd --add-port=8080/tcp --permanent` | Open permanent port |
| `firewall-cmd --remove-port=8080/tcp` | Close runtime port |
| `firewall-cmd --remove-port=8080/tcp --permanent` | Close permanent port |
| `firewall-cmd --query-port=8080/tcp` | Check a port rule |
| `firewall-cmd --list-ports` | List allowed ports |
| `firewall-cmd --add-port=30000-32767/tcp` | Open a port range |
| `ss -tulpn` | Check listening applications |
| `nc -zv HOST PORT` | Test a remote port |
| `curl HOST:PORT` | Test an HTTP application |

---

# Conclusion

Port rules are used when an application does not have a suitable predefined firewalld service.

The essential troubleshooting model is:

```text
Application
    |
    v
Listening Port
    |
    v
Firewall Rule
    |
    v
Remote Client
```

Always verify both the application and the firewall before concluding that a network port is working.