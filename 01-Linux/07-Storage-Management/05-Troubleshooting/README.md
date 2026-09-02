# 🛠️ Storage Troubleshooting

> Diagnosing disk space, filesystem, and storage performance problems in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Troubleshooting Flow](#troubleshooting-flow)
- [Common Problems](#common-problems)
- [Topics](#topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `df -h` | Show filesystem usage |
| `du -sh` | Show directory size |
| `lsblk -f` | Show storage layout |
| `findmnt` | Show mounted filesystems |
| `blkid` | Show UUID and filesystem type |
| `df -i` | Check inode usage |
| `iostat` | Check disk I/O |
| `dmesg` | Check kernel storage errors |
| `journalctl -k` | Inspect kernel logs |

---

## Troubleshooting Flow

```mermaid
flowchart LR

    A["❌ Storage Problem"] --> B["💽 Disk Visible?<br>lsblk"]
    B --> C["📂 Mounted?<br>findmnt"]
    C --> D["📊 Space Available?<br>df -h"]
    D --> E["📁 What Uses Space?<br>du"]
    E --> F["🧱 Filesystem Healthy?<br>fsck / xfs_repair"]
    F --> G["⚡ Performance OK?<br>iostat"]
```

---

## Common Problems

Typical storage incidents include:

- Filesystem is full
- Inodes are exhausted
- Disk is missing
- Mount disappeared
- Filesystem becomes read-only
- Filesystem corruption
- Slow disk I/O
- A directory is consuming unexpected space

A good starting point is:

```bash
lsblk -f
df -h
df -i
findmnt
```

---

## Topics

```text
05-Troubleshooting/
├── README.md
├── disk-space.md
├── filesystem-errors.md
└── performance.md
```

### `disk-space.md`

Diagnose:

```text
No space left on device
```

using:

```bash
df
du
find
```

### `filesystem-errors.md`

Investigate:

- Corruption
- Read-only filesystems
- Failed mounts
- XFS/ext4 errors

### `performance.md`

Diagnose:

- Slow disks
- High I/O wait
- Busy storage devices

using tools such as:

```bash
iostat
iotop
```

---

## Key Principle

Troubleshoot from the storage layer upward:

```text
Disk
 ↓
Partition / LVM
 ↓
Filesystem
 ↓
Mount
 ↓
Free Space
 ↓
Application
```

---

## Conclusion

Storage troubleshooting becomes much easier when you first determine whether the problem is caused by:

```text
Capacity
Filesystem
Mounting
or
Performance
```