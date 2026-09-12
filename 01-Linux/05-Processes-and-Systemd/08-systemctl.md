# 🎛️ systemctl - Managing systemd Units

> Controlling services and other systemd units from the command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Service Management](#service-management)
- [Enable vs Start](#enable-vs-start)
- [Inspecting Units](#inspecting-units)
- [Practical Examples](#practical-examples)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl status SERVICE` | Show service status |
| `systemctl start SERVICE` | Start service |
| `systemctl stop SERVICE` | Stop service |
| `systemctl restart SERVICE` | Restart service |
| `systemctl reload SERVICE` | Reload config |
| `systemctl enable SERVICE` | Enable at boot |
| `systemctl disable SERVICE` | Disable at boot |
| `systemctl enable --now SERVICE` | Enable and start |
| `systemctl is-active SERVICE` | Check runtime state |
| `systemctl is-enabled SERVICE` | Check boot state |

---

## Overview

`systemctl` is the main command used to manage `systemd`.

It can control:

```text
Services
Timers
Sockets
Mounts
Targets
```

Example:

```bash
systemctl status sshd
```

---

## Service Management

Start:

```bash
sudo systemctl start nginx
```

Stop:

```bash
sudo systemctl stop nginx
```

Restart:

```bash
sudo systemctl restart nginx
```

Reload configuration without a full restart:

```bash
sudo systemctl reload nginx
```

This only works if the service supports reload.

---

## Enable vs Start

These are different:

```text
start
→ run now

enable
→ start automatically at boot
```

Example:

```bash
sudo systemctl start nginx
```

does not necessarily enable nginx at boot.

Enable:

```bash
sudo systemctl enable nginx
```

Do both:

```bash
sudo systemctl enable --now nginx
```

---

## Inspecting Units

Check status:

```bash
systemctl status nginx
```

Check if active:

```bash
systemctl is-active nginx
```

Check if enabled:

```bash
systemctl is-enabled nginx
```

List failed units:

```bash
systemctl --failed
```

List running services:

```bash
systemctl list-units --type=service
```

---

## Practical Examples

Restart SSH:

```bash
sudo systemctl restart sshd
```

Disable service from boot:

```bash
sudo systemctl disable nginx
```

Show unit file:

```bash
systemctl cat nginx
```

Reload systemd after editing a unit:

```bash
sudo systemctl daemon-reload
```

---

## Troubleshooting

### Service Fails to Start

Check:

```bash
systemctl status SERVICE
```

Then:

```bash
journalctl -u SERVICE
```

### Unit File Changed but Nothing Happened

Run:

```bash
sudo systemctl daemon-reload
```

Then restart the unit:

```bash
sudo systemctl restart SERVICE
```

---

## Related Topics

- `systemd.md`
- `journalctl.md`
- `troubleshooting.md`
- `signals.md`

---

## Conclusion

`systemctl` controls the lifecycle and boot behavior of systemd units.

The key distinction is:

```text
start  → now
enable → next boot
```

For most service administration, `systemctl status`, `restart`, and `enable --now` are the commands used most often.