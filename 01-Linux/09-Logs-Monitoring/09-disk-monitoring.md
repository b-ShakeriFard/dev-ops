# 💽 Disk Monitoring

> Checking filesystem usage, directory growth, inode usage, and storage performance.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Filesystem Usage](#filesystem-usage)
- [Directory Usage](#directory-usage)
- [Inode Usage](#inode-usage)
- [Disk Performance](#disk-performance)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `df -h` | Filesystem space usage |
| `df -i` | Inode usage |
| `du -sh PATH` | Directory size |
| `du -sh *` | Compare directory sizes |
| `lsblk` | Show disks and partitions |
| `iostat` | Disk I/O statistics |
| `iotop` | Processes causing disk I/O |

---

## Overview

Disk problems usually fall into two categories:

```text
Capacity problem
        ↓
Filesystem is full

Performance problem
        ↓
Disk I/O is slow or overloaded
```

Both can cause applications and services to fail.

---

## Filesystem Usage

Check mounted filesystems:

```bash
df -h
```

Important columns:

```text
Size
Used
Avail
Use%
Mounted on
```

Example:

```text
/dev/sda2   100G   95G   5G   95%   /
```

A nearly full root filesystem deserves investigation.

---

## Directory Usage

Find large directories:

```bash
du -sh /var/*
```

Sort by size:

```bash
du -sh /var/* | sort -h
```

Check one directory:

```bash
du -sh /var/log
```

For interactive investigation, if installed:

```bash
ncdu /var
```

---

## Inode Usage

A filesystem can have free disk space but still fail to create files if it runs out of inodes.

Check:

```bash
df -i
```

High inode usage often means there are huge numbers of small files.

---

## Disk Performance

If available:

```bash
iostat -xz 2
```

Useful fields include:

```text
%util  → device utilization
await  → average I/O wait time
r/s    → reads per second
w/s    → writes per second
```

Find I/O-heavy processes:

```bash
sudo iotop
```

---

## Practical Examples

Find the largest areas under `/var`:

```bash
du -sh /var/* | sort -h
```

Check logs:

```bash
du -sh /var/log/*
```

Find large files:

```bash
find /var -type f -size +1G -ls
```

Check filesystems and devices:

```bash
df -h
lsblk
```

---

## Common Pitfalls

### df and du Show Different Values

A deleted file may still be held open by a running process.

Check:

```bash
lsof +L1
```

### Ignoring Inodes

Always check both:

```bash
df -h
df -i
```

### Deleting Files Immediately

First identify why storage is growing before removing data.

---

## Related Topics

- `monitoring-basics.md`
- `cpu-memory.md`
- `logrotate.md`
- `process-monitoring.md`
- `troubleshooting.md`

---

## Conclusion

For disk troubleshooting, begin with:

```bash
df -h
df -i
du -sh
```

Then use `iostat` or `iotop` when the issue appears to be performance rather than capacity.