# 🗂️ Linux Log Files

> Understanding common log files under `/var/log` and what they contain.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Common Log Files](#common-log-files)
- [Distribution Differences](#distribution-differences)
- [Reading Log Files](#reading-log-files)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Path | Purpose |
|---|---|
| `/var/log/syslog` | General system logs on Debian/Ubuntu |
| `/var/log/messages` | General system logs on RHEL/Rocky |
| `/var/log/auth.log` | Authentication logs on Debian/Ubuntu |
| `/var/log/secure` | Authentication logs on RHEL/Rocky |
| `/var/log/cron` | Cron activity |
| `/var/log/audit/` | Audit logs |
| `/var/log/nginx/` | Nginx logs |
| `/var/log/httpd/` | Apache logs on RHEL-style systems |

---

## Overview

Traditional Linux logs are usually stored under:

```text
/var/log
```

These logs may come from:

```text
System services
Authentication
Scheduled jobs
Applications
Security tools
```

---

## Common Log Files

General system events:

```text
/var/log/syslog
/var/log/messages
```

Authentication:

```text
/var/log/auth.log
/var/log/secure
```

Cron:

```text
/var/log/cron
```

Audit subsystem:

```text
/var/log/audit/audit.log
```

Application-specific examples:

```text
/var/log/nginx/
/var/log/httpd/
/var/log/mysql/
```

---

## Distribution Differences

Debian/Ubuntu commonly use:

```text
/var/log/syslog
/var/log/auth.log
```

RHEL/Rocky commonly use:

```text
/var/log/messages
/var/log/secure
```

Always check the actual system instead of assuming a path exists.

---

## Reading Log Files

View file:

```bash
less /var/log/messages
```

Show last lines:

```bash
tail /var/log/messages
```

Follow live:

```bash
tail -f /var/log/messages
```

Search for errors:

```bash
grep -i error /var/log/messages
```

Search compressed rotated logs:

```bash
zgrep -i error /var/log/messages-*.gz
```

---

## Practical Examples

Watch SSH authentication:

```bash
tail -f /var/log/secure
```

On Ubuntu:

```bash
tail -f /var/log/auth.log
```

Search failed logins:

```bash
grep -i failed /var/log/secure
```

Inspect Nginx errors:

```bash
tail -f /var/log/nginx/error.log
```

---

## Common Pitfalls

### Permission Denied

Some logs require root privileges:

```bash
sudo less /var/log/secure
```

### Log File Does Not Exist

The system may use:

```text
journalctl
```

instead of a traditional file, or the distribution may use another filename.

### Old Logs Missing

Logs may have been rotated by:

```text
logrotate
```

Check for files such as:

```text
messages.1
messages-20260912
messages.1.gz
```

---

## Related Topics

- `logs-overview.md`
- `rsyslog.md`
- `logrotate.md`
- `journalctl.md`

---

## Conclusion

The most important directory is:

```text
/var/log
```

The key skill is knowing:

```text
Which log
+
Which command
+
Which filter
```

to use for the problem you are investigating.