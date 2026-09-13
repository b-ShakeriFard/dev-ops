# 📁 mkdir

> Creating directories from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Directories](#creating-directories)
- [Creating Parent Directories](#creating-parent-directories)
- [Useful Options](#useful-options)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `mkdir DIR` | Create directory |
| `mkdir DIR1 DIR2` | Create multiple directories |
| `mkdir -p PATH` | Create parent directories as needed |
| `mkdir -v DIR` | Verbose output |
| `mkdir -m 755 DIR` | Set permissions at creation |

---

## Overview

`mkdir` means:

```text
make directory
```

Basic syntax:

```bash
mkdir DIRECTORY
```

Example:

```bash
mkdir project
```

This creates a new directory named:

```text
project
```

---

## Creating Directories

Create one directory:

```bash
mkdir logs
```

Create multiple directories:

```bash
mkdir dev test prod
```

Check:

```bash
ls
```

---

## Creating Parent Directories

Suppose you want:

```text
project/config/nginx
```

If the parent directories do not exist, this fails:

```bash
mkdir project/config/nginx
```

Use:

```bash
mkdir -p project/config/nginx
```

`-p` creates missing parent directories automatically.

This is one of the most useful `mkdir` options.

---

## Useful Options

Verbose mode:

```bash
mkdir -v project
```

Example output:

```text
mkdir: created directory 'project'
```

Set permissions:

```bash
mkdir -m 750 secure-dir
```

Check:

```bash
ls -ld secure-dir
```

---

## Practical Examples

Create project structure:

```bash
mkdir -p app/{config,logs,data}
```

Result:

```text
app/
├── config/
├── logs/
└── data/
```

Create nested backup directory:

```bash
mkdir -p /tmp/backups/database
```

Create several numbered directories:

```bash
mkdir server{1..3}
```

Result:

```text
server1
server2
server3
```

---

## Common Pitfalls

### Directory Already Exists

Running:

```bash
mkdir project
```

when it exists gives an error.

Using:

```bash
mkdir -p project
```

does not complain if it already exists.

### Permission Denied

Example:

```bash
mkdir /opt/test
```

may require elevated privileges:

```bash
sudo mkdir /opt/test
```

### Forgetting Quotes

For names containing spaces:

```bash
mkdir "My Project"
```

---

## Related Topics

- `rmdir.md`
- `touch.md`
- `rm.md`
- `../01-Navigation/cd.md`

---

## Conclusion

The two most useful patterns are:

```bash
mkdir DIR
mkdir -p PATH
```

For scripts and automation, `mkdir -p` is especially useful because it safely creates missing directory structures.