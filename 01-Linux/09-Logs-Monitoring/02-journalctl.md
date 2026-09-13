# 📚 journalctl

> Querying and filtering logs stored by the systemd journal.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Viewing Logs](#viewing-logs)
- [Filtering by Service](#filtering-by-service)
- [Filtering by Time](#filtering-by-time)
- [Filtering by Priority](#filtering-by-priority)
- [Following Logs](#following-logs)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `journalctl` | Show journal logs |
| `journalctl -u SERVICE` | Logs for one service |
| `journalctl -b` | Current boot logs |
| `journalctl -b -1` | Previous boot |
| `journalctl -f` | Follow logs live |
| `journalctl -p err` | Show errors |
| `journalctl --since today` | Logs since today |
| `journalctl -n 50` | Last 50 entries |

---

## Overview

`journalctl` reads logs collected by:

```text
systemd-journald
```

It can contain messages from:

```text
Services
Kernel
Boot process
Applications
System components
```

---

## Viewing Logs

Show everything:

```bash
journalctl
```

Newest entries first:

```bash
journalctl -r
```

Last 50 messages:

```bash
journalctl -n 50
```

Current boot only:

```bash
journalctl -b
```

Previous boot:

```bash
journalctl -b -1
```

---

## Filtering by Service

Example:

```bash
journalctl -u nginx
```

SSH service:

```bash
journalctl -u sshd
```

Show only recent entries:

```bash
journalctl -u nginx -n 20
```

---

## Filtering by Time

Since today:

```bash
journalctl --since today
```

Since one hour ago:

```bash
journalctl --since "1 hour ago"
```

Between two times:

```bash
journalctl \
  --since "2026-09-13 10:00" \
  --until "2026-09-13 11:00"
```

---

## Filtering by Priority

Show errors:

```bash
journalctl -p err
```

Useful priority levels:

```text
emerg
alert
crit
err
warning
notice
info
debug
```

Example:

```bash
journalctl -p warning
```

This includes warning and more severe messages.

---

## Following Logs

Live monitoring:

```bash
journalctl -f
```

Follow one service:

```bash
journalctl -u nginx -f
```

Very useful while restarting or testing a service.

---

## Practical Examples

Investigate failed service:

```bash
systemctl status nginx
journalctl -u nginx -n 50
```

Show kernel logs:

```bash
journalctl -k
```

Show errors from current boot:

```bash
journalctl -b -p err
```

Check journal disk usage:

```bash
journalctl --disk-usage
```

---

## Common Pitfalls

### Wrong Service Name

Check available units:

```bash
systemctl list-units --type=service
```

### Too Much Output

Use filters:

```bash
-u
-b
--since
-p
-n
```

rather than reading the entire journal.

---

## Related Topics

- `logs-overview.md`
- `rsyslog.md`
- `dmesg.md`
- `troubleshooting.md`

---

## Conclusion

The most useful pattern is:

```bash
journalctl -u SERVICE -n 50
```

For live troubleshooting:

```bash
journalctl -u SERVICE -f
```

`journalctl` is one of the first tools to reach for when a systemd service misbehaves.