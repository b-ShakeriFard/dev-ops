# 📁 find

> Searching the filesystem by name, type, size, age, permissions, and other properties.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Searching by Name](#searching-by-name)
- [Searching by Type](#searching-by-type)
- [Searching by Size and Time](#searching-by-size-and-time)
- [Executing Commands](#executing-commands)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `find PATH -name NAME` | Search by name |
| `find PATH -iname NAME` | Case-insensitive name |
| `find PATH -type f` | Find files |
| `find PATH -type d` | Find directories |
| `find PATH -size +1G` | Larger than 1 GB |
| `find PATH -mtime +7` | Modified over 7 days ago |
| `find PATH -user USER` | Owned by user |
| `find PATH -perm MODE` | Search permissions |
| `find ... -exec CMD {} \;` | Run command per result |

---

## Overview

`find` searches the filesystem directly.

Basic syntax:

```bash
find PATH CONDITIONS
```

Example:

```bash
find /etc -name "sshd_config"
```

Unlike `locate`, `find` checks the filesystem live.

---

## Searching by Name

Exact pattern:

```bash
find /var/log -name "*.log"
```

Case-insensitive:

```bash
find /etc -iname "*.CONF"
```

Search from current directory:

```bash
find . -name "*.sh"
```

---

## Searching by Type

Regular files:

```bash
find /etc -type f
```

Directories:

```bash
find /var -type d
```

Symbolic links:

```bash
find /usr/local -type l
```

Combine conditions:

```bash
find /var/log -type f -name "*.log"
```

---

## Searching by Size and Time

Files larger than 1 GB:

```bash
find /var -type f -size +1G
```

Files smaller than 10 MB:

```bash
find /tmp -type f -size -10M
```

Modified more than 7 days ago:

```bash
find /var/log -type f -mtime +7
```

Modified within the last day:

```bash
find /tmp -type f -mtime -1
```

---

## Executing Commands

Run a command on each result:

```bash
find . -name "*.log" -exec ls -lh {} \;
```

Delete matching files:

```bash
find /tmp -type f -name "*.tmp" -delete
```

Be extremely careful with `-delete`.

Test first:

```bash
find /tmp -type f -name "*.tmp"
```

---

## Practical Examples

Find configuration files:

```bash
find /etc -type f -name "*.conf"
```

Find files owned by a user:

```bash
find /home -user alice
```

Find world-writable files:

```bash
find /var -type f -perm -0002
```

Find large files and show details:

```bash
find /var -type f -size +500M -exec ls -lh {} \;
```

Find old logs:

```bash
find /var/log -type f -name "*.log" -mtime +30
```

---

## Common Pitfalls

### Permission Denied

Searching large trees may produce errors:

```text
Permission denied
```

Use:

```bash
sudo find ...
```

when appropriate.

### Forgetting Quotes

Prefer:

```bash
find . -name "*.log"
```

not:

```bash
find . -name *.log
```

The shell may expand the wildcard before `find` receives it.

### Dangerous `-delete`

Always preview matches first.

### Searching from `/`

This can be slow:

```bash
find /
```

Use the narrowest useful starting path.

---

## Related Topics

- `grep.md`
- `locate.md`
- `../05-Text-Processing/xargs.md`
- `../../../09-Logs-Monitoring/disk-monitoring.md`

---

## Conclusion

The core pattern is:

```bash
find PATH -type TYPE -name PATTERN
```

Then add filters such as:

```text
size
time
user
permissions
```

`find` is one of the most flexible filesystem-search tools in Linux.