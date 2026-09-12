# 🗂️ Linux Filesystem Hierarchy

> Understanding the purpose of the most important Linux directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Important Directories](#important-directories)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/etc` | System configuration |
| `/home` | User home directories |
| `/root` | Root user's home |
| `/var` | Logs and changing application data |
| `/usr` | Programs, libraries, shared data |
| `/tmp` | Temporary files |
| `/dev` | Device files |
| `/proc` | Process and kernel information |
| `/sys` | Kernel/device information |
| `/boot` | Bootloader and kernel files |
| `/opt` | Optional software |
| `/mnt` | Temporary mounts |
| `/media` | Removable media |

---

## Overview

Linux uses a single directory tree beginning at:

```text
/
```

Everything exists somewhere beneath this root:

```text
/
├── etc
├── home
├── usr
├── var
├── dev
└── ...
```

Different filesystems can be mounted into this same tree.

---

## Important Directories

### `/etc`

System configuration:

```text
/etc/ssh/
/etc/fstab
/etc/hosts
```

### `/home`

Normal user data:

```text
/home/alice
/home/bob
```

### `/var`

Frequently changing data:

```text
/var/log
/var/lib
/var/cache
```

Very important for servers and container platforms.

### `/usr`

Most installed applications and libraries:

```text
/usr/bin
/usr/sbin
/usr/lib
```

### `/dev`

Represents devices:

```text
/dev/sda
/dev/null
/dev/tty
```

### `/proc`

Virtual filesystem exposing process and kernel information:

```text
/proc/cpuinfo
/proc/meminfo
/proc/1234
```

### `/boot`

Contains boot-related files such as kernels and bootloader data.

---

## Practical Examples

Check your location:

```bash
pwd
```

Explore the root:

```bash
ls /
```

Inspect configuration:

```bash
ls /etc
```

Check logs:

```bash
ls /var/log
```

Check mounted filesystems:

```bash
findmnt
```

---

## Key Mental Model

```text
/
├── Configuration → /etc
├── Users         → /home
├── Applications  → /usr
├── Logs/Data     → /var
├── Devices       → /dev
└── Kernel Info   → /proc, /sys
```

---

## Related Topics

- `absolute-vs-relative-paths.md`
- `file-types.md`
- `../07-Storage/`

---

## Conclusion

The Linux filesystem hierarchy gives directories predictable responsibilities.

Knowing where configuration, logs, applications, users, and device information live makes administration and troubleshooting much faster.