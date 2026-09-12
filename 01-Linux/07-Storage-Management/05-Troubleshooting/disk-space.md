# 📊 Disk Space Troubleshooting

> Finding where storage space is being consumed and why a filesystem is full.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Practical Workflow](#practical-workflow)
- [Inode Problems](#inode-problems)
- [Common Scenarios](#common-scenarios)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `df -h` | Show filesystem usage |
| `df -i` | Show inode usage |
| `du -sh DIR` | Show directory size |
| `du -xhd1 /` | Show top-level usage on one filesystem |
| `find / -type f -size +1G` | Find very large files |
| `sort -h` | Sort sizes |
| `lsof +L1` | Find deleted but still-open files |

---

## Overview

The classic error:

```text
No space left on device
```

does not always mean the disk itself is completely full.

Possible causes:

- Filesystem is full
- Inodes are exhausted
- Large log files
- Deleted files still held open by a process
- Unexpected data stored under a mount point

---

## Practical Workflow

### 1. Check Filesystem Usage

```bash
df -h
```

Example:

```text
Filesystem      Size  Used  Avail Use%
/dev/sda2       100G   96G     4G  96%
```

Focus on:

```text
Use%
```

---

### 2. Find the Large Directory

Start at the affected filesystem.

Example:

```bash
sudo du -xhd1 /
```

Then drill down:

```bash
sudo du -xhd1 /var
```

Example:

```text
2G   /var/lib
18G  /var/log
```

Now investigate:

```bash
sudo du -xhd1 /var/log
```

---

### 3. Find Large Files

Example:

```bash
sudo find /var \
-type f \
-size +1G \
-exec ls -lh {} \;
```

Or:

```bash
sudo find / \
-xdev \
-type f \
-size +1G
```

---

## Inode Problems

A filesystem can have free disk space but still fail to create new files.

Check:

```bash
df -i
```

Example:

```text
Filesystem    IUse% 
/dev/sda2      100%
```

This means:

```text
All inodes are used.
```

Usually caused by:

- Millions of small files
- Cache files
- Session files
- Temporary files

Find directories with many files:

```bash
sudo find /var -xdev -type f | wc -l
```

---

## Deleted Files Still Using Space

Sometimes:

```bash
rm huge.log
```

does not immediately free space.

Why?

A running process may still have the file open.

Check:

```bash
sudo lsof +L1
```

You may see:

```text
java  1234  /var/log/app.log (deleted)
```

Restart the responsible service:

```bash
sudo systemctl restart <service>
```

Then check:

```bash
df -h
```

---

## Common Scenarios

### `/var/log` Is Full

Check:

```bash
sudo du -sh /var/log/*
```

Inspect journal usage:

```bash
journalctl --disk-usage
```

Reduce old journal logs:

```bash
sudo journalctl --vacuum-time=7d
```

---

### Container Storage Is Full

Common locations:

```text
/var/lib/docker
/var/lib/containers
/var/lib/containerd
```

Check:

```bash
sudo du -sh /var/lib/*
```

Do not manually delete container runtime data unless you understand what owns it.

---

### `df` and `du` Show Different Usage

Example:

```text
df says 90G used

du finds only 60G
```

Possible cause:

```text
Deleted file still open
```

Check:

```bash
sudo lsof +L1
```

---

### Mounted Filesystem Hides Existing Data

Suppose files existed under:

```text
/data
```

Then another filesystem was mounted on `/data`.

The old files still consume space but are hidden by the mount.

Check mounts:

```bash
findmnt /data
```

Unmount carefully and inspect the underlying directory.

---

## Useful Size Commands

Largest directories:

```bash
du -h /var | sort -h | tail
```

Top-level filesystem usage:

```bash
sudo du -xhd1 /
```

Largest files:

```bash
sudo find /var \
-type f \
-printf '%s %p\n' \
| sort -n \
| tail
```

---

## Safe Troubleshooting Order

```text
df -h
   ↓
df -i
   ↓
du
   ↓
find
   ↓
lsof +L1
```

Do not start deleting files before identifying what is actually consuming the space.

---

## Related Topics

- `filesystem-errors.md`
- `performance.md`
- `../04-Mounting/troubleshooting.md`
- `../03-LVM/resize.md`

---

## Conclusion

When a Linux filesystem is full, first determine whether the problem is:

```text
Disk blocks
Inodes
Large files
Hidden files
or
Deleted open files
```

The most useful starting commands are:

```bash
df -h
df -i
du -xhd1 /
lsof +L1
```