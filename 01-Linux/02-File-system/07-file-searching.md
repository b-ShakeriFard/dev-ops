# 🔎 Searching for Files in Linux

> Finding files, directories, and commands efficiently.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using find](#using-find)
- [Using locate](#using-locate)
- [Finding Commands](#finding-commands)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `find PATH -name NAME` | Search by name |
| `find PATH -type f` | Find files |
| `find PATH -type d` | Find directories |
| `find PATH -size +100M` | Find large files |
| `locate NAME` | Fast database-based search |
| `which COMMAND` | Show executable path |
| `whereis COMMAND` | Find binary, source, and man page |
| `command -v COMMAND` | Show command location |

---

## Overview

Linux provides several search tools.

The most important distinction:

```text
find
→ searches the filesystem directly

locate
→ searches a prebuilt database
```

For commands and executables, use:

```text
which
whereis
command -v
```

---

## Using find

Search by name:

```bash
find /etc -name "hosts"
```

Case-insensitive:

```bash
find /etc -iname "HOSTS"
```

Find files only:

```bash
find /var -type f
```

Find directories:

```bash
find /var -type d
```

---

## Search by Size

Find files larger than 1 GB:

```bash
find /var -type f -size +1G
```

Find files smaller than 10 MB:

```bash
find /tmp -type f -size -10M
```

---

## Search by Modification Time

Modified during the last 24 hours:

```bash
find /var/log -type f -mtime -1
```

Modified more than 30 days ago:

```bash
find /tmp -type f -mtime +30
```

---

## Using locate

Search quickly:

```bash
locate ssh_config
```

Because `locate` uses a database, recent files may not appear until the database is updated:

```bash
sudo updatedb
```

---

## Finding Commands

Executable path:

```bash
which python3
```

More portable:

```bash
command -v python3
```

Find binary and documentation:

```bash
whereis ssh
```

---

## Practical Examples

Find SSH keys:

```bash
find ~/.ssh -type f
```

Find large log files:

```bash
sudo find /var/log \
-type f \
-size +500M
```

Find configuration files:

```bash
find /etc -name "*.conf"
```

Find executable:

```bash
command -v kubectl
```

---

## Common Pitfall

This:

```bash
find /
```

can generate huge output and permission errors.

Usually, start with a narrower path:

```bash
find /etc
find /var
find ~
```

---

## Related Topics

- `filesystem-hierarchy.md`
- `absolute-vs-relative-paths.md`
- `hidden-files.md`
- `../01-Basic-CLI/find.md`

---

## Conclusion

Use the right tool for the job:

```text
find      → accurate filesystem search
locate    → fast name lookup
which     → executable path
whereis   → binary + docs
command -v → command resolution
```

For day-to-day administration, `find` is the most powerful and flexible option.