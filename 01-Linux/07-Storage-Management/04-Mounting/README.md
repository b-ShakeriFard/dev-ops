# 📂 Linux Mounting

> Attaching filesystems to the Linux directory tree and making mounts persistent.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Mounting Workflow](#mounting-workflow)
- [Topics](#topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `mount` | Show mounted filesystems |
| `mount DEVICE DIR` | Mount a filesystem |
| `umount DIR` | Unmount a filesystem |
| `blkid` | Show UUIDs |
| `mount -a` | Mount entries from `/etc/fstab` |
| `findmnt` | Show mount relationships |

---

## Overview

Linux does not assign drive letters like Windows.

Instead, storage is attached to directories called **mount points**.

Example:

```text
/dev/sdb1
   ↓
/data
```

Once mounted, files stored on `/dev/sdb1` become accessible through:

```text
/data
```

---

## Mounting Workflow

```mermaid
flowchart LR

    A["💽 Filesystem<br>/dev/sdb1"] --> B["📂 Mount Point<br>/data"]
    B --> C["⚙️ mount"]
    C --> D["📦 Application Data"]
    D --> E["💾 /etc/fstab<br>Persistent Mount"]
```

Typical workflow:

```text
Create filesystem
      ↓
Create mount point
      ↓
Mount filesystem
      ↓
Verify
      ↓
Add to /etc/fstab
```

---

## Topics

```text
04-Mounting/
├── README.md
├── fstab.md
├── automount.md
└── troubleshooting.md
```

### `fstab.md`

Persistent filesystem mounts using:

```text
/etc/fstab
```

### `automount.md`

Mount filesystems automatically when they are accessed.

### `troubleshooting.md`

Diagnose:

- Failed mounts
- Wrong UUIDs
- Busy filesystems
- Boot problems caused by `/etc/fstab`

---

## Key Idea

Temporary mount:

```bash
mount /dev/sdb1 /data
```

Persistent mount:

```text
/etc/fstab
```

Mount configuration should always be tested before rebooting:

```bash
mount -a
```

---

## Conclusion

Linux mounting connects storage devices to the filesystem tree.

The essential model is:

```text
Filesystem
   ↓
Mount Point
   ↓
Persistent Configuration
```