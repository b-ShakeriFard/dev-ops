# 📜 journalctl - systemd Logs

> Viewing system and service logs from the systemd journal.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Service Logs](#service-logs)
- [Time-Based Filtering](#time-based-filtering)
- [Boot Logs](#boot-logs)
- [Practical Examples](#practical-examples)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `journalctl` | Show journal logs |
| `journalctl -u SERVICE` | Show service logs |
| `journalctl -f` | Follow logs live |
| `journalctl -b` | Show current boot logs |
| `journalctl -b -1` | Show previous boot |
| `journalctl --since today` | Show today's logs |
| `journalctl -p err` | Show error-level logs |
| `journalctl -k` | Show kernel logs |

---

## Overview

`journalctl` reads logs collected by:

```text
systemd-journald
```

The journal can contain:

- Service logs
- Kernel messages
- Boot events
- Authentication events
- systemd messages

Basic command:

```bash
journalctl
```

---

## Service Logs

Show logs for nginx:

```bash
journalctl -u nginx
```

Follow live:

```bash
journalctl -u nginx -f
```

Show recent lines:

```bash
journalctl -u nginx -n 50
```

This is often the first command to run after:

```bash
systemctl status nginx
```

---

## Time-Based Filtering

Logs since today:

```bash
journalctl --since today
```

Last hour:

```bash
journalctl --since "1 hour ago"
```

Specific range:

```bash
journalctl \
--since "2026-09-13 10:00" \
--until "2026-09-13 11:00"
```

---

## Boot Logs

Current boot:

```bash
journalctl -b
```

Previous boot:

```bash
journalctl -b -1
```

List available boots:

```bash
journalctl --list-boots
```

Useful when a problem happened before the latest reboot.

---

## Filter by Priority

Errors:

```bash
journalctl -p err
```

Warnings and worse:

```bash
journalctl -p warning
```

Kernel logs:

```bash
journalctl -k
```

---

## Practical Examples

Service failed:

```bash
systemctl status sshd
journalctl -u sshd -n 50
```

Watch a restarting service:

```bash
journalctl -u myapp -f
```

Inspect boot failure:

```bash
journalctl -b -p err
```

---

## Troubleshooting

### Logs Are Too Large

Narrow by:

```text
Service
Time
Boot
Priority
```

Example:

```bash
journalctl \
-u nginx \
--since "10 minutes ago"
```

### No Logs Found

Check the service name:

```bash
systemctl list-units --type=service
```

Then retry with the correct unit.

---

## Related Topics

- `systemd.md`
- `systemctl.md`
- `troubleshooting.md`
- `../09-Logs-Monitoring/`

---

## Conclusion

`journalctl` is the main log-reading tool for systemd systems.

The most useful patterns are:

```bash
journalctl -u SERVICE
journalctl -u SERVICE -f
journalctl -b
journalctl -p err
```

When a service fails, pair `systemctl status` with `journalctl`.