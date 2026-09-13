# 📅 date & cal

> Viewing and formatting date, time, and calendar information in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using date](#using-date)
- [Formatting date](#formatting-date)
- [Using cal](#using-cal)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `date` | Show current date and time |
| `date +%F` | Show `YYYY-MM-DD` |
| `date +%T` | Show `HH:MM:SS` |
| `date +%s` | Unix timestamp |
| `date -u` | Show UTC time |
| `cal` | Show current month |
| `cal 2026` | Show full year |
| `cal 9 2026` | Show September 2026 |

---

## Overview

`date` displays system date and time.

Example:

```bash
date
```

Typical output:

```text
Sun Sep 13 17:40:12 +0330 2026
```

`cal` displays a calendar.

```bash
cal
```

---

## Using date

Current date and time:

```bash
date
```

UTC:

```bash
date -u
```

Unix timestamp:

```bash
date +%s
```

This returns the number of seconds since:

```text
1970-01-01 00:00:00 UTC
```

---

## Formatting date

`date` supports format codes.

Common ones:

```text
%Y → year
%m → month
%d → day
%H → hour
%M → minute
%S → second
%F → YYYY-MM-DD
%T → HH:MM:SS
```

Examples:

```bash
date +%F
```

```text
2026-09-13
```

Custom format:

```bash
date '+%Y-%m-%d_%H-%M-%S'
```

Useful for filenames:

```bash
backup-$(date +%F).tar.gz
```

---

## Using cal

Current month:

```bash
cal
```

Specific month:

```bash
cal 9 2026
```

Full year:

```bash
cal 2026
```

Show three months:

```bash
cal -3
```

---

## Practical Examples

Create timestamped log file:

```bash
LOG="app-$(date +%F).log"
```

Create precise timestamp:

```bash
STAMP=$(date '+%Y%m%d-%H%M%S')
```

Check current timezone-aware time:

```bash
date
```

Show UTC for troubleshooting:

```bash
date -u
```

---

## Common Pitfalls

### Wrong System Time

If `date` shows incorrect time, the problem may be:

```text
Timezone
NTP synchronization
Manual clock settings
VM clock drift
```

### `cal` Not Installed

Some distributions do not include `cal` by default.

It may be provided by a package such as:

```text
util-linux
```

depending on the distribution.

### Confusing `%m` and `%M`

```text
%m → month
%M → minute
```

---

## Related Topics

- `hostname-uname.md`
- `whoami-id.md`
- `uptime.md`
- `../../../09-Logs-Monitoring/`

---

## Conclusion

For day-to-day work, these are the most useful forms:

```bash
date
date +%F
date '+%Y-%m-%d_%H-%M-%S'
cal
```

`date` is especially useful in scripts, logs, backups, and troubleshooting.