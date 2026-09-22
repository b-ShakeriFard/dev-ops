# ⚡ locate

> Finding files and directories quickly using a prebuilt filename database.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Updating the Database](#updating-the-database)
- [Useful Options](#useful-options)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `locate NAME` | Search paths by name |
| `locate -i NAME` | Case-insensitive search |
| `locate -b NAME` | Match basename only |
| `locate -c NAME` | Count matches |
| `locate -e NAME` | Show only existing paths |
| `updatedb` | Refresh locate database |

---

## Overview

`locate` searches a database of filenames and paths.

Example:

```bash
locate sshd_config
```

Possible output:

```text
/etc/ssh/sshd_config
/usr/share/man/man5/sshd_config.5.gz
```

Because it searches a database instead of scanning the filesystem directly, it is usually much faster than:

```bash
find
```

---

## Basic Usage

Search for a filename:

```bash
locate nginx.conf
```

Search for part of a path:

```bash
locate /nginx/
```

Case-insensitive search:

```bash
locate -i README
```

Count results:

```bash
locate -c ssh
```

---

## Updating the Database

The database may not contain recently created files.

Refresh it with:

```bash
sudo updatedb
```

Then search again:

```bash
locate new-file.txt
```

The database is often updated automatically by a scheduled system task, but the timing depends on the distribution.

---

## Useful Options

Match only the final filename component:

```bash
locate -b '\sshd_config'
```

Show only paths that currently exist:

```bash
locate -e nginx.conf
```

Limit output:

```bash
locate -l 10 nginx
```

This shows only the first 10 matches.

---

## Practical Examples

Find SSH configuration:

```bash
locate sshd_config
```

Find Kubernetes files:

```bash
locate kubeconfig
```

Find shared libraries:

```bash
locate libssl.so
```

Find `.conf` files containing nginx in the path:

```bash
locate nginx | grep '\.conf$'
```

Combine with `less`:

```bash
locate python | less
```

---

## Common Pitfalls

### File Exists but locate Cannot Find It

The database may be stale.

Run:

```bash
sudo updatedb
```

### Deleted Files Still Appear

Again, the database may be outdated.

Use:

```bash
locate -e NAME
```

to display only paths that still exist.

### locate May Not Be Installed

On some systems it is provided by packages such as:

```text
plocate
mlocate
```

depending on the distribution.

### locate vs find

Use:

```text
locate → fast filename/path lookup
find   → live filesystem search with conditions
```

`find` is better when you need filters such as:

```text
Size
Owner
Permissions
Modification time
File type
```

---

## Related Topics

- `grep.md`
- `find.md`
- `../05-Text-Processing/`
- `../../../09-Logs-Monitoring/`

---

## Conclusion

The basic pattern is:

```bash
locate NAME
```

If results appear stale:

```bash
sudo updatedb
```

Use `locate` for speed and `find` when you need precise live filesystem filtering.