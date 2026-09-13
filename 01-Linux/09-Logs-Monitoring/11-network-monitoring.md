# 🌐 Network Monitoring

> Checking interfaces, connections, ports, traffic, and network-related problems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Interfaces](#interfaces)
- [Sockets and Ports](#sockets-and-ports)
- [Connectivity](#connectivity)
- [Traffic Monitoring](#traffic-monitoring)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ip addr` | Show interfaces and IP addresses |
| `ip route` | Show routing table |
| `ss -tulpn` | Show listening sockets |
| `ss -tan` | Show TCP connections |
| `ping HOST` | Test reachability |
| `curl URL` | Test application endpoint |
| `tcpdump` | Capture packets |
| `sar -n DEV 1` | Interface traffic statistics |

---

## Overview

Network monitoring helps answer:

```text
Is the interface up?
Does the server have an IP?
Is the route correct?
Is the service listening?
Can traffic reach the host?
```

A useful workflow is:

```text
Interface
   ↓
IP address
   ↓
Route
   ↓
Port
   ↓
Application
```

---

## Interfaces

Show interfaces:

```bash
ip addr
```

Show link state:

```bash
ip link
```

Typical states:

```text
UP
DOWN
```

Check routes:

```bash
ip route
```

The default route often looks like:

```text
default via 192.168.1.1 dev eth0
```

---

## Sockets and Ports

Show listening TCP/UDP ports:

```bash
ss -tulpn
```

Check one port:

```bash
ss -ltnp | grep :443
```

Show active TCP connections:

```bash
ss -tan
```

Useful states include:

```text
LISTEN
ESTAB
TIME-WAIT
```

---

## Connectivity

Test host reachability:

```bash
ping -c 4 server01
```

Test HTTP:

```bash
curl -I http://server01
```

Test a specific TCP port:

```bash
nc -zv server01 443
```

---

## Traffic Monitoring

If `sysstat` is installed:

```bash
sar -n DEV 1
```

This shows interface traffic over time.

For packet-level inspection:

```bash
sudo tcpdump -i eth0
```

Filter by port:

```bash
sudo tcpdump -i eth0 port 443
```

---

## Practical Examples

Find which process owns port 8080:

```bash
ss -ltnp | grep :8080
```

Check routing:

```bash
ip route
```

Test application connectivity:

```bash
curl -v http://server01:8080
```

Watch DNS traffic:

```bash
sudo tcpdump -i any port 53
```

---

## Common Pitfalls

### Ping Works but Application Fails

ICMP reachability does not mean the application port is open.

Check:

```bash
ss
nc
curl
```

### Service Running but Not Reachable

Verify:

```text
Listening address
Firewall
Route
Port
Application configuration
```

### Wrong Interface

Use:

```bash
ip route get DESTINATION
```

to see which interface Linux will use.

---

## Related Topics

- `monitoring-basics.md`
- `process-monitoring.md`
- `uptime-load.md`
- `troubleshooting.md`

---

## Conclusion

For network monitoring, start with:

```bash
ip addr
ip route
ss -tulpn
ping
curl
```

Then move to `tcpdump` when you need packet-level visibility.