# 🧱 Filesystem Error Troubleshooting

> Diagnosing filesystem corruption, read-only mounts, and repair issues.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Read-Only Filesystems](#read-only-filesystems)
- [EXT4 Repair](#ext4-repair)
- [XFS Repair](#xfs-repair)
- [Kernel Logs](#kernel-logs)
- [Common Scenarios](#common-scenarios)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `lsblk -f` | Show filesystem type |
| `findmnt` | Show mount status/options |
| `dmesg` | Check kernel storage errors |
| `journalctl -k` | Inspect kernel logs |
| `fsck` | Check/repair ext filesystems |
| `e2fsck` | EXT filesystem checker |
| `xfs_repair` | Repair XFS |
| `mount -o remount,rw` | Attempt read-write remount |

---

## Overview

Filesystem problems may appear as:

- Filesystem mounted read-only
- Files cannot be created
- Mount fails
- I/O errors
- Boot enters emergency mode
- Filesystem corruption is reported

The first question is:

```text
Is this a filesystem problem,
or is the underlying disk failing?
```

---

## Read-Only Filesystems

Check mount options:

```bash
findmnt /data
```

Look for:

```text
ro
```

instead of:

```text
rw
```

Check kernel messages:

```bash
dmesg | tail -50
```

or:

```bash
journalctl -k
```

A filesystem may become read-only after serious I/O or consistency errors.

---

## Attempt a Read-Write Remount

Example:

```bash
sudo mount -o remount,rw /data
```

Then verify:

```bash
findmnt /data
```

If it immediately returns to read-only mode, investigate the filesystem and disk rather than repeatedly remounting it.

---

## EXT4 Repair

Check filesystem type first:

```bash
lsblk -f
```

For EXT4, unmount the filesystem:

```bash
sudo umount /dev/sdb1
```

Check:

```bash
sudo fsck /dev/sdb1
```

or:

```bash
sudo e2fsck /dev/sdb1
```

Force a check:

```bash
sudo e2fsck -f /dev/sdb1
```

Interactive repair:

```bash
sudo fsck /dev/sdb1
```

Automatic yes:

```bash
sudo fsck -y /dev/sdb1
```

⚠️ Use `-y` carefully because changes are accepted automatically.

---

## XFS Repair

XFS uses:

```bash
xfs_repair
```

not normal `fsck`.

Unmount first:

```bash
sudo umount /data
```

Then:

```bash
sudo xfs_repair /dev/sdb1
```

Verify afterward:

```bash
sudo mount /dev/sdb1 /data
```

---

## XFS Log Problems

Sometimes XFS reports a dirty or damaged log.

In severe cases:

```bash
sudo xfs_repair -L /dev/sdb1
```

⚠️ `-L` clears the XFS journal and can cause data loss.

Use it only when normal repair cannot proceed and recovery options are understood.

---

## Kernel Logs

Storage failures often appear in kernel logs.

Check:

```bash
dmesg | grep -i error
```

or:

```bash
journalctl -k
```

Useful terms:

```text
I/O error
Buffer I/O error
EXT4-fs error
XFS error
reset
timeout
read-only
```

---

## Common Scenarios

### Filesystem Suddenly Becomes Read-Only

Check:

```bash
findmnt /
journalctl -k
```

Possible causes:

- Filesystem corruption
- Disk I/O failure
- Storage path failure
- Virtual disk/backend issue

---

### `fsck` Says Filesystem Is Mounted

Unmount it first:

```bash
sudo umount /dev/sdb1
```

Then:

```bash
sudo fsck /dev/sdb1
```

Do not repair a normal mounted read-write filesystem.

---

### XFS Repair Command Does Nothing

Check filesystem type:

```bash
lsblk -f
```

If filesystem is EXT4, use:

```bash
fsck
```

If filesystem is XFS, use:

```bash
xfs_repair
```

---

### Mount Fails After Repair

Check:

```bash
blkid /dev/sdb1
```

Then:

```bash
mount -v /dev/sdb1 /data
```

Inspect:

```bash
journalctl -k
```

---

## Check the Underlying Device

Filesystem corruption may only be the symptom.

Check device visibility:

```bash
lsblk
```

Check kernel errors:

```bash
dmesg
```

For physical disks, SMART tools may also help:

```bash
smartctl -a /dev/sda
```

---

## Safe Troubleshooting Order

```text
Identify filesystem
        ↓
Check mount state
        ↓
Check kernel logs
        ↓
Unmount safely
        ↓
Run correct repair tool
        ↓
Mount and verify
```

---

## Related Topics

- `disk-space.md`
- `performance.md`
- `../02-Filesystems/ext4.md`
- `../02-Filesystems/xfs.md`
- `../04-Mounting/troubleshooting.md`

---

## Conclusion

The most important rule is to use the repair tool that matches the filesystem:

```text
EXT4 → fsck / e2fsck

XFS  → xfs_repair
```

Always check kernel logs as well, because filesystem errors may be caused by a deeper disk or storage failure.