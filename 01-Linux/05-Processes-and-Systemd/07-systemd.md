# ⚙️ systemd

> Understanding the service and system manager used by most modern Linux distributions.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Units](#units)
- [Service Lifecycle](#service-lifecycle)
- [Targets](#targets)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl status SERVICE` | Check service status |
| `systemctl start SERVICE` | Start service |
| `systemctl stop SERVICE` | Stop service |
| `systemctl restart SERVICE` | Restart service |
| `systemctl reload SERVICE` | Reload configuration |
| `systemctl enable SERVICE` | Enable at boot |
| `systemctl disable SERVICE` | Disable at boot |
| `systemctl list-units` | Show active units |

---

## Overview

`systemd` is the init and service manager used by many modern Linux distributions.

It starts early during boot and manages:

```text
Services
Sockets
Mounts
Timers
Devices
Targets
```

The main command used to control it is:

```bash
systemctl
```

---

## Units

systemd manages resources using **units**.

Common unit types:

| Unit | Purpose |
|---|---|
| `.service` | Service or daemon |
| `.socket` | Socket activation |
| `.target` | Group of units |
| `.mount` | Filesystem mount |
| `.timer` | Scheduled activation |

Example:

```text
nginx.service
```

---

## Service Lifecycle

A service may move through states such as:

```text
inactive
   ↓
activating
   ↓
active
   ↓
deactivating
   ↓
inactive
```

Check:

```bash
systemctl status nginx
```

---

## Targets

Targets group units into system states.

Examples:

```text
multi-user.target
graphical.target
rescue.target
```

Check default target:

```bash
systemctl get-default
```

Set:

```bash
sudo systemctl set-default multi-user.target
```

---

## Practical Examples

Start SSH:

```bash
sudo systemctl start sshd
```

Enable at boot:

```bash
sudo systemctl enable sshd
```

Do both:

```bash
sudo systemctl enable --now sshd
```

Check failed units:

```bash
systemctl --failed
```

List services:

```bash
systemctl list-units --type=service
```

---

## Unit Files

Common locations:

```text
/etc/systemd/system/
/usr/lib/systemd/system/
```

After changing unit files:

```bash
sudo systemctl daemon-reload
```

Then restart the service if needed.

---

## Related Topics

- `systemctl.md`
- `journalctl.md`
- `signals.md`
- `troubleshooting.md`

---

## Conclusion

`systemd` manages the lifecycle of Linux services and other system resources.

The essential relationship is:

```text
systemd
   ↓
Unit
   ↓
Service / Mount / Timer / Socket
```

For day-to-day administration, `systemctl` is the primary interface to systemd.