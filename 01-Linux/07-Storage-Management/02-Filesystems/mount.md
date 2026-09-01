# 📂 mount - Attach Filesystems

> Making a filesystem accessible through a directory.

---

# 🎯 What Problem Does It Solve?

A filesystem exists on a disk, but Linux applications cannot use it until it is mounted.

Example:

```
/dev/sdb1

    ↓

/data

    ↓

Application files
```

---

# Basic Syntax

```bash
mount DEVICE MOUNT_POINT
```

Example:

```bash
mount /dev/sdb1 /data
```

---

# Create Mount Point

A mount point is simply a directory.

Example:

```bash
mkdir /data
```

Mount:

```bash
mount /dev/sdb1 /data
```

Verify:

```bash
df -h
```

Example:

```
Filesystem   Size  Mounted on

/dev/sdb1    500G  /data
```

---

# View Mounted Filesystems

```bash
mount
```

or:

```bash
df -h
```

Better filesystem view:

```bash
lsblk -f
```

Example:

```
NAME   FSTYPE MOUNTPOINT

sdb1   xfs    /data
```

---

# Mount by UUID

Using device names:

```
/dev/sdb1
```

can change.

UUIDs are more reliable.

Find UUID:

```bash
blkid
```

Example:

```
/dev/sdb1 UUID="abcd-1234"
```

Mount:

```bash
mount UUID=abcd-1234 /data
```

---

# Unmount Filesystem

Syntax:

```bash
umount DEVICE
```

Example:

```bash
umount /dev/sdb1
```

or:

```bash
umount /data
```

Check:

```bash
mount | grep sdb1
```

---

# Persistent Mounting with /etc/fstab

Normal mount:

```bash
mount /dev/sdb1 /data
```

disappears after reboot.

For permanent mounting:

```text
/etc/fstab
```

Example:

```
UUID=abcd-1234  /data  xfs  defaults  0 0
```

Test:

```bash
mount -a
```

If no errors appear:

```text
fstab configuration is valid
```

---

# Filesystem Types

Usually Linux detects the type automatically.

You can specify it:

```bash
mount -t xfs /dev/sdb1 /data
```

Examples:

```bash
mount -t ext4 /dev/sdb1 /data
```

```bash
mount -t xfs /dev/sdb1 /data
```

---

# Real Example

New disk:

```
/dev/sdb1

500GB XFS
```

Create mount:

```bash
mkdir /database
```

Mount:

```bash
mount /dev/sdb1 /database
```

Verify:

```bash
df -h /database
```

Make permanent:

```bash
blkid
```

Add UUID to:

```text
/etc/fstab
```

Test:

```bash
mount -a
```

---

# Troubleshooting

## Mount fails

Check filesystem:

```bash
blkid /dev/sdb1
```

Check device:

```bash
lsblk
```

---

## Device is busy

Find processes:

```bash
lsof /data
```

or:

```bash
fuser -m /data
```

Unmount after stopping processes:

```bash
umount /data
```

---

## fstab Error After Reboot

Test before reboot:

```bash
mount -a
```

Check:

- UUID
- filesystem type
- mount directory

---

# ☸️ Kubernetes Connection

Linux mounting is the foundation of container volumes.

Linux:

```
Filesystem

    ↓

Mount Point

    ↓

Application
```

Kubernetes:

```
Persistent Volume

    ↓

Volume Mount

    ↓

Container
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `mount` | Show mounted filesystems |
| `mount DEVICE DIR` | Mount filesystem |
| `umount DEVICE` | Unmount filesystem |
| `df -h` | Show disk usage |
| `lsblk -f` | Show filesystem layout |
| `blkid` | Show UUID |
| `mount -a` | Test fstab |
| `lsof /path` | Find users of mount |

---

# Conclusion

Mounting connects storage to the Linux filesystem tree.

The basic workflow:

```
Partition

↓

Filesystem

↓

Mount Point

↓

Application Data
```

For production systems, always use UUIDs in `/etc/fstab` to ensure reliable mounts after reboot.