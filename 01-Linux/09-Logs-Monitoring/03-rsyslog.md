# 📡 rsyslog

> Collecting, filtering, and routing traditional Linux system logs.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Configuration Files](#configuration-files)
- [Facilities and Priorities](#facilities-and-priorities)
- [Basic Rules](#basic-rules)
- [Service Management](#service-management)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command / Path | Purpose |
|---|---|
| `systemctl status rsyslog` | Check rsyslog |
| `/etc/rsyslog.conf` | Main configuration |
| `/etc/rsyslog.d/` | Additional configuration |
| `logger "message"` | Send test log |
| `rsyslogd -N1` | Validate configuration |
| `systemctl restart rsyslog` | Restart service |

---

## Overview

`rsyslog` is a logging daemon used to receive and route system messages.

Typical flow:

```text
Application / Service
        ↓
     rsyslog
        ↓
   /var/log/*
```

It can also forward logs to remote log servers.

---

## Configuration Files

Main configuration:

```text
/etc/rsyslog.conf
```

Additional rules:

```text
/etc/rsyslog.d/*.conf
```

Using separate files under `/etc/rsyslog.d/` is usually easier to manage.

---

## Facilities and Priorities

Syslog messages are commonly classified by:

```text
Facility
+
Priority
```

Common facilities:

```text
auth
authpriv
cron
daemon
kern
mail
user
local0-local7
```

Common priorities:

```text
debug
info
notice
warning
err
crit
alert
emerg
```

---

## Basic Rules

Typical syntax:

```text
FACILITY.PRIORITY    DESTINATION
```

Example:

```text
authpriv.*    /var/log/secure
```

This means:

```text
All authpriv messages
        ↓
/var/log/secure
```

Another example:

```text
*.err    /var/log/errors.log
```

---

## Service Management

Check status:

```bash
systemctl status rsyslog
```

Validate configuration:

```bash
sudo rsyslogd -N1
```

Restart after changes:

```bash
sudo systemctl restart rsyslog
```

---

## Practical Examples

Send a test message:

```bash
logger "Test message from $(hostname)"
```

Then search:

```bash
grep "Test message" /var/log/messages
```

On Debian/Ubuntu:

```bash
grep "Test message" /var/log/syslog
```

Create a custom rule:

```text
local0.*    /var/log/myapp.log
```

Then generate:

```bash
logger -p local0.info "Application test"
```

---

## Common Pitfalls

### Wrong Log File

Log locations differ between distributions.

For example:

```text
RHEL/Rocky → /var/log/messages
Ubuntu     → /var/log/syslog
```

### Configuration Changed but Nothing Happened

Validate:

```bash
rsyslogd -N1
```

Then restart:

```bash
systemctl restart rsyslog
```

### File Permissions

rsyslog must be able to write to the target file or directory.

---

## Related Topics

- `logs-overview.md`
- `journalctl.md`
- `log-files.md`
- `logrotate.md`

---

## Conclusion

`rsyslog` provides rule-based traditional logging.

The key model is:

```text
Message
  ↓
Facility + Priority
  ↓
rsyslog rule
  ↓
Log file or remote destination
```

It remains important on many enterprise Linux systems.