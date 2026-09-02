# ⚙️ Automount

> Mounting filesystems automatically when they are accessed.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [How Automount Works](#how-automount-works)
- [Practical Example](#practical-example)
- [Troubleshooting](#troubleshooting)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl status autofs` | Check autofs |
| `systemctl enable --now autofs` | Start and enable autofs |
| `/etc/auto.master` | Main automount configuration |
| `/etc/auto.*` | Mount map files |
| `mount` | Verify active mount |
| `findmnt` | Show mounted filesystems |

---

## Overview

`autofs` mounts filesystems automatically when their directory is accessed.

Example:

```text
User accesses:

/mnt/share

      ↓

autofs mounts filesystem

      ↓

Files become available
```

When the mount is no longer used, `autofs` can automatically unmount it.

This is especially useful for:

- NFS shares
- Network storage
- Occasionally used filesystems
- Large environments with many mounts

---

## How Automount Works

The main configuration file is:

```text
/etc/auto.master
```

It points to one or more map files.

Example:

```text
/mnt  /etc/auto.storage
```

Meaning:

```text
/mnt
  ↓
Configuration stored in
/etc/auto.storage
```

---

## Install autofs

Enterprise Linux:

```bash
sudo dnf install autofs
```

Debian/Ubuntu:

```bash
sudo apt install autofs
```

Start:

```bash
sudo systemctl enable --now autofs
```

Check:

```bash
systemctl status autofs
```

---

## Practical Example

Suppose an NFS server provides:

```text
192.168.1.20:/data
```

Goal:

```text
/mnt/storage
```

### Configure auto.master

Add:

```text
/mnt  /etc/auto.storage
```

### Create the Map File

```bash
sudo vi /etc/auto.storage
```

Add:

```text
storage  -fstype=nfs  192.168.1.20:/data
```

Restart:

```bash
sudo systemctl restart autofs
```

---

## Trigger the Mount

Access:

```bash
ls /mnt/storage
```

This triggers the mount automatically.

Verify:

```bash
findmnt /mnt/storage
```

or:

```bash
mount | grep storage
```

---

## Direct vs Automount

Traditional `/etc/fstab`:

```text
Boot
 ↓
Mount filesystem
 ↓
Keep mounted
```

`autofs`:

```text
Access directory
 ↓
Mount filesystem
 ↓
Use filesystem
 ↓
Automatically unmount later
```

---

## Timeout

Automount can unmount inactive filesystems after a timeout.

Example in `/etc/auto.master`:

```text
/mnt  /etc/auto.storage  --timeout=300
```

Meaning:

```text
300 seconds idle
      ↓
Unmount
```

---

## Troubleshooting

### Mount Does Not Trigger

Check service:

```bash
systemctl status autofs
```

Restart:

```bash
sudo systemctl restart autofs
```

---

### Configuration Error

Check:

```bash
journalctl -u autofs
```

Also inspect:

```bash
cat /etc/auto.master
cat /etc/auto.storage
```

---

### NFS Server Unreachable

Test:

```bash
ping 192.168.1.20
```

Check exported shares:

```bash
showmount -e 192.168.1.20
```

---

### Mount Works Manually but Not with autofs

Try:

```bash
mount -t nfs \
192.168.1.20:/data \
/mnt/test
```

If manual mounting works, inspect the autofs configuration.

---

## Related Topics

- `fstab.md`
- `troubleshooting.md`
- `../02-Filesystems/mount.md`

---

## Conclusion

`autofs` is useful when filesystems should be mounted only when needed.

The basic workflow is:

```text
Configure auto.master
      ↓
Create map file
      ↓
Start autofs
      ↓
Access directory
      ↓
Filesystem mounts automatically
```

For always-needed local storage, `/etc/fstab` is usually simpler.

For on-demand or network storage, `autofs` can be more flexible.