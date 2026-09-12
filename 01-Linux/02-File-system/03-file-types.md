# 📄 Linux File Types

> Understanding the different kinds of filesystem objects Linux uses.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Main File Types](#main-file-types)
- [How to Identify Them](#how-to-identify-them)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Type | Symbol | Example |
|---|---:|---|
| Regular file | `-` | `/etc/hosts` |
| Directory | `d` | `/var/log` |
| Symbolic link | `l` | `/bin → /usr/bin` |
| Block device | `b` | `/dev/sda` |
| Character device | `c` | `/dev/tty` |
| Socket | `s` | Service communication |
| Named pipe | `p` | FIFO |

---

## Overview

In Linux, many different objects are represented as files.

This includes:

- Normal files
- Directories
- Disks
- Terminals
- Sockets
- Pipes

This is part of the classic Linux idea:

```text
"Everything is a file"
```

---

## Main File Types

### Regular File

Contains data such as:

```text
Text
Binary
Configuration
Scripts
```

Example:

```text
/etc/hosts
```

---

### Directory

Contains references to other filesystem objects.

Example:

```text
/home
/var/log
/etc
```

---

### Symbolic Link

Points to another path.

Example:

```text
link → /opt/application
```

Created with:

```bash
ln -s TARGET LINK
```

---

### Block Device

Represents block-oriented storage.

Examples:

```text
/dev/sda
/dev/nvme0n1
```

Commonly used for disks and partitions.

---

### Character Device

Handles data as a stream.

Examples:

```text
/dev/tty
/dev/null
/dev/random
```

---

### Socket

Used for communication between processes.

Common example:

```text
/var/run/*.sock
```

---

### Named Pipe

Allows processes to exchange data through a filesystem object.

Created with:

```bash
mkfifo mypipe
```

---

## How to Identify Them

Use:

```bash
ls -l
```

Example:

```text
drwxr-xr-x  directory
-rw-r--r--  file
lrwxrwxrwx  symlink
```

The first character identifies the type.

You can also use:

```bash
file NAME
```

Example:

```bash
file /bin/bash
```

---

## Practical Examples

Inspect a directory:

```bash
ls -ld /etc
```

Inspect a device:

```bash
ls -l /dev/sda
```

Inspect a link:

```bash
ls -l /bin
```

Identify file content:

```bash
file /etc/passwd
```

---

## Related Topics

- `links.md`
- `hidden-files.md`
- `filesystem-hierarchy.md`

---

## Conclusion

Linux files are not limited to documents and programs.

The filesystem also represents:

```text
Directories
Devices
Sockets
Pipes
Links
```

Understanding file types makes Linux filesystem behavior much easier to interpret.