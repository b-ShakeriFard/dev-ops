# 🛠️ Mounting Troubleshooting

> Diagnosing failed mounts, bad `/etc/fstab` entries, and busy filesystems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Common Problems](#common-problems)
- [fstab Problems](#fstab-problems)
- [Busy Filesystems](#busy-filesystems)
- [Troubleshooting Workflow](#troubleshooting-workflow)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `lsblk -f` | Show devices, filesystems, and mounts |
| `blkid` | Show UUID and filesystem type |
| `findmnt` | Show mount relationships |
| `mount -a` | Test `/etc/fstab` |
| `findmnt --verify` | Validate mount configuration |
| `df -h` | Show mounted filesystem usage |
| `lsof /path` | Find processes using a mount |
| `fuser -m /path` | Find users of a filesystem |
| `journalctl -b` | Inspect boot/mount errors |

---

## Common Problems

### Wrong Device or UUID

Check:

```bash
lsblk -f
```

and:

```bash
blkid
```

Compare with:

```bash
cat /etc/fstab
```

---

### Wrong Filesystem Type

Check:

```bash
blkid /dev/sdb1
```

Example:

```text
TYPE="xfs"
```

The matching `/etc/fstab` entry must use:

```text
xfs
```

---

### Mount Point Does Not Exist

Example:

```text
mount point does not exist
```

Fix:

```bash
sudo mkdir -p /data
```

Then:

```bash
sudo mount -a
```

---

### Filesystem Is Already Mounted

Check:

```bash
findmnt /data
```

or:

```bash
mount | grep /data
```

---

## fstab Problems

A bad `/etc/fstab` entry can cause:

- Mount failures
- Boot delays
- Emergency mode

After editing `/etc/fstab`, always test:

```bash
sudo mount -a
```

Also validate:

```bash
findmnt --verify
```

A safe workflow is:

```text
Edit /etc/fstab
      ↓
mount -a
      ↓
findmnt --verify
      ↓
Verify mount
      ↓
Reboot
```

---

### Optional Disk Missing at Boot

For non-critical storage, consider:

```text
nofail
```

Example:

```text
UUID=abcd-1234  /backup  xfs  defaults,nofail  0  0
```

This allows boot to continue if the device is unavailable.

---

## Busy Filesystems

Unmount fails:

```text
target is busy
```

Find processes:

```bash
lsof /data
```

or:

```bash
fuser -m /data
```

Check whether your shell itself is inside the mount:

```bash
pwd
```

Move elsewhere:

```bash
cd /
```

Then retry:

```bash
sudo umount /data
```

---

## Mount Works Manually but Not After Reboot

Manual mount:

```bash
mount /dev/sdb1 /data
```

works, but reboot does not.

Check:

```bash
cat /etc/fstab
```

Verify:

- UUID
- Mount point
- Filesystem type
- Mount options

Test:

```bash
mount -a
```

---

## Filesystem Appears Mounted but Space Is Wrong

Check:

```bash
df -h /data
```

Compare:

```bash
lsblk
```

You may have mounted a filesystem over a directory that already contained files.

Unmount temporarily:

```bash
sudo umount /data
```

Then inspect:

```bash
ls -lah /data
```

---

## Read-Only Filesystem

Check mount options:

```bash
findmnt /data
```

Look for:

```text
ro
```

Kernel errors may also cause a filesystem to become read-only.

Check:

```bash
dmesg | tail
```

or:

```bash
journalctl -k
```

---

## Automount Problems

Check:

```bash
systemctl status autofs
```

Inspect logs:

```bash
journalctl -u autofs
```

Restart:

```bash
sudo systemctl restart autofs
```

Then trigger the mount:

```bash
ls /mnt/storage
```

---

## Troubleshooting Workflow

```text
Mount Failed
    ↓
lsblk -f
    ↓
Check UUID / filesystem
    ↓
Check mount point
    ↓
Check /etc/fstab
    ↓
mount -a
    ↓
findmnt --verify
    ↓
Check logs
```

Useful final checks:

```bash
lsblk -f
findmnt
df -h
journalctl -b
```

---

## Related Topics

- `fstab.md`
- `automount.md`
- `../02-Filesystems/mount.md`
- `../05-Troubleshooting/filesystem-errors.md`

---

## Conclusion

Most mounting problems come from:

```text
Wrong device
Wrong UUID
Wrong filesystem
Missing directory
Busy mount
Bad /etc/fstab entry
```

Start with:

```bash
lsblk -f
```

and work upward from the storage device to the mount configuration.