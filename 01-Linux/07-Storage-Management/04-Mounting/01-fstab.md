# 📌 /etc/fstab

> Defining filesystems that should be mounted automatically.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [fstab Structure](#fstab-structure)
- [Practical Examples](#practical-examples)
- [Testing Changes](#testing-changes)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `cat /etc/fstab` | View persistent mounts |
| `blkid` | Find filesystem UUID |
| `lsblk -f` | Show UUID and filesystem type |
| `mount -a` | Test and mount fstab entries |
| `findmnt --verify` | Check fstab configuration |
| `findmnt` | Show mounted filesystems |

---

## Overview

`/etc/fstab` defines filesystems that Linux should mount automatically.

Example:

```text
UUID=abcd-1234  /data  xfs  defaults  0  0
```

This means:

```text
Filesystem
    ↓
Mount at /data
    ↓
Use XFS
    ↓
Mount automatically
```

---

## fstab Structure

Each line contains six fields:

```text
DEVICE  MOUNT_POINT  TYPE  OPTIONS  DUMP  PASS
```

Example:

```text
UUID=abcd-1234  /data  xfs  defaults  0  0
```

| Field | Purpose |
|---|---|
| `UUID=...` | Filesystem to mount |
| `/data` | Mount point |
| `xfs` | Filesystem type |
| `defaults` | Mount options |
| `0` | Dump backup flag |
| `0` | Filesystem check order |

---

## Why Use UUID?

You could use:

```text
/dev/sdb1
```

But device names may change.

UUIDs are more reliable:

```bash
blkid /dev/sdb1
```

Example:

```text
UUID="abcd-1234"
TYPE="xfs"
```

---

## Practical Examples

### XFS Mount

```text
UUID=abcd-1234  /data  xfs  defaults  0  0
```

### EXT4 Mount

```text
UUID=efgh-5678  /backup  ext4  defaults  0  2
```

### Read-Only Mount

```text
UUID=abcd-1234  /archive  xfs  ro  0  0
```

---

## Common Mount Options

| Option | Meaning |
|---|---|
| `defaults` | Standard mount options |
| `ro` | Read-only |
| `rw` | Read-write |
| `noexec` | Prevent execution of binaries |
| `nosuid` | Ignore SUID/SGID bits |
| `nodev` | Ignore device files |
| `nofail` | Continue boot if mount fails |

Example:

```text
UUID=abcd-1234  /data  xfs  defaults,nofail  0  0
```

---

## Testing Changes

Never reboot immediately after editing `/etc/fstab`.

Test first:

```bash
sudo mount -a
```

If no error appears, the configuration is usually valid.

You can also check:

```bash
findmnt --verify
```

Then verify:

```bash
findmnt /data
```

---

## Practical Workflow

Find the UUID:

```bash
blkid
```

Create mount point:

```bash
mkdir -p /data
```

Edit:

```bash
sudo vi /etc/fstab
```

Add:

```text
UUID=abcd-1234  /data  xfs  defaults  0  0
```

Test:

```bash
sudo mount -a
```

Verify:

```bash
df -h /data
```

---

## Troubleshooting

### Wrong UUID

Check:

```bash
blkid
```

Compare with:

```bash
cat /etc/fstab
```

---

### Mount Point Missing

Create it:

```bash
mkdir -p /data
```

Then:

```bash
mount -a
```

---

### Wrong Filesystem Type

Check:

```bash
lsblk -f
```

Example:

```text
sdb1  xfs
```

Ensure `/etc/fstab` also specifies:

```text
xfs
```

---

### Boot Delays Because Disk Is Missing

For optional storage, consider:

```text
nofail
```

Example:

```text
UUID=abcd-1234  /backup  xfs  defaults,nofail  0  0
```

---

## Related Topics

- `automount.md`
- `troubleshooting.md`
- `../02-Filesystems/mount.md`

---

## Conclusion

`/etc/fstab` makes filesystem mounts persistent across reboots.

The safe workflow is:

```text
Find UUID
   ↓
Edit /etc/fstab
   ↓
mount -a
   ↓
Verify
   ↓
Reboot only after testing
```