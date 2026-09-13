# 🔄 logrotate

> Automatically rotating, compressing, and removing old log files.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Configuration Files](#configuration-files)
- [Common Directives](#common-directives)
- [Practical Example](#practical-example)
- [Testing logrotate](#testing-logrotate)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command / Path | Purpose |
|---|---|
| `/etc/logrotate.conf` | Main configuration |
| `/etc/logrotate.d/` | Per-application rules |
| `logrotate -d CONFIG` | Debug without changes |
| `logrotate -f CONFIG` | Force rotation |
| `rotate 7` | Keep 7 old logs |
| `daily` | Rotate daily |
| `compress` | Compress old logs |
| `missingok` | Ignore missing files |

---

## Overview

Log files grow continuously.

Without rotation:

```text
Application
    ↓
log file grows
    ↓
disk fills
    ↓
system problems
```

`logrotate` prevents this by:

```text
Rotate
Compress
Retain
Delete
```

old logs automatically.

---

## Configuration Files

Main configuration:

```text
/etc/logrotate.conf
```

Application-specific rules:

```text
/etc/logrotate.d/
```

Example:

```bash
ls /etc/logrotate.d/
```

You may see entries for:

```text
nginx
httpd
rsyslog
dnf
```

---

## Common Directives

Example:

```text
daily
rotate 7
compress
delaycompress
missingok
notifempty
```

Meaning:

```text
daily         → rotate every day
rotate 7      → keep 7 rotations
compress      → gzip old logs
delaycompress → delay compression by one cycle
missingok     → no error if log is missing
notifempty    → do not rotate empty logs
```

---

## Practical Example

Create:

```text
/etc/logrotate.d/myapp
```

Configuration:

```text
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
}
```

This keeps approximately seven rotated copies.

---

## Testing logrotate

Always test first:

```bash
sudo logrotate -d /etc/logrotate.conf
```

This shows what would happen without modifying files.

Force rotation:

```bash
sudo logrotate -f /etc/logrotate.conf
```

Use `-f` carefully.

---

## postrotate

Some applications must reopen their log file after rotation.

Example:

```text
postrotate
    systemctl reload nginx
endscript
```

This runs after the log has been rotated.

---

## Common Pitfalls

### Application Still Writes to Old Log

The application may need:

```text
reload
```

or another mechanism to reopen its log files.

### Logs Deleted Too Soon

Check:

```text
rotate
daily
weekly
monthly
```

settings before changing retention.

### Rotation Does Not Happen

Debug with:

```bash
logrotate -d /etc/logrotate.conf
```

Also check file permissions and configuration syntax.

---

## Related Topics

- `log-files.md`
- `rsyslog.md`
- `journalctl.md`
- `disk-monitoring.md`

---

## Conclusion

`logrotate` manages the lifecycle of traditional log files:

```text
Active Log
    ↓
Rotate
    ↓
Compress
    ↓
Retain
    ↓
Delete
```

It is essential for preventing logs from consuming excessive disk space.