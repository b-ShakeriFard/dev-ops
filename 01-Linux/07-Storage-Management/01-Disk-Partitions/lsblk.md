# 💾 lsblk - List Block Devices

> Viewing disks, partitions, and storage relationships in Linux.

---

# 🎯 What Problem Does It Solve?

Before managing storage, administrators need to know:

- What disks exist?
- Which partitions exist?
- Which devices are mounted?
- Which disks belong to LVM?
- How is storage connected?

`lsblk` provides a simple view of the Linux block device hierarchy.

---

# 🧩 What Is a Block Device?

A block device is storage that Linux can read and write in blocks.

Examples:

```
Physical Disk:

/dev/sda


Partition:

/dev/sda1


Virtual Disk:

/dev/vdb


NVMe Disk:

/dev/nvme0n1
```

---

# 🔎 Basic Syntax

```bash
lsblk
```

Example output:

```
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS

sda           8:0    0   100G  0 disk

├─sda1        8:1    0     1G  0 part /boot

└─sda2        8:2    0    99G  0 part /
```

---

# 📖 Understanding the Output

Example:

```
sda

|

+-- sda1

|

+-- sda2
```

Meaning:

```
Disk

/dev/sda

   |

   +-- Partition

       /dev/sda1

   |

   +-- Partition

       /dev/sda2
```

---

# 📋 Important Columns

Default output:

```bash
lsblk
```

Columns:

| Column | Meaning |
|-|-|
| NAME | Device name |
| SIZE | Device size |
| TYPE | Disk or partition |
| MOUNTPOINT | Mounted location |
| RM | Removable device |
| RO | Read-only |

---

# 🔍 Show Filesystem Information

By default:

```bash
lsblk
```

does not always show filesystem details.

Use:

```bash
lsblk -f
```

Example:

```
NAME   FSTYPE LABEL UUID

sda1   xfs          xxxx

sda2   xfs          yyyy
```

Shows:

- Filesystem type
- UUID
- Labels

---

# 📍 Show Full Device Paths

Default:

```
sda
```

Full path:

```bash
lsblk -p
```

Example:

```
/dev/sda

/dev/sda1

/dev/sda2
```

Useful when using:

```bash
mount
mkfs
fdisk
```

---

# 📊 Show Device Information

Use:

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINT
```

Example:

```
NAME   SIZE TYPE FSTYPE MOUNTPOINT

sda    100G disk

sda1     1G part xfs    /boot

sda2    99G part xfs    /
```

This is often the most useful format.

---

# 💽 Example: New Disk Added

A server receives:

```
New 500GB disk
```

Run:

```bash
lsblk
```

Output:

```
sda     100G disk
├─sda1    1G part
└─sda2   99G part

sdb     500G disk
```

Interpretation:

```
Existing OS disk:

/dev/sda


New empty disk:

/dev/sdb
```

---

# 🧱 Before Partitioning

Before:

```
/dev/sdb

500G disk
```

After:

```
/dev/sdb

 |

 +-- /dev/sdb1
```

Verify:

```bash
lsblk
```

---

# 🔥 LVM Example

`lsblk` also shows LVM relationships.

Example:

```
sdb

└─sdb1

   └─rhel-root
```

Meaning:

```
Disk

 |

Partition

 |

Logical Volume
```

Full storage path:

```
/dev/sdb1

      |

      v

Volume Group

      |

      v

Logical Volume
```

---

# 🖥️ Real Rocky Linux Example

Check server storage:

```bash
lsblk -f
```

Output:

```
NAME        FSTYPE MOUNTPOINT

sda

├─sda1      xfs    /boot

└─sda2      xfs    /

sdb
```

Administrator conclusion:

```
Operating system:

/dev/sda


Available storage:

/dev/sdb
```

Next steps:

```
Partition /dev/sdb

↓

Create filesystem

↓

Mount storage
```

---

# 🔗 Related Commands

## Show filesystem UUID

```bash
blkid
```

---

## Show mounted filesystems

```bash
df -h
```

---

## Show partitions

```bash
fdisk -l
```

---

## Show block devices

```bash
lsblk
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## New Disk Not Visible

Check:

```bash
lsblk
```

If missing:

Check kernel messages:

```bash
dmesg | tail
```

Possible causes:

- Disk not attached
- VM disk not added
- Storage controller issue

---

## Problem 2:
## Disk Exists But No Mount Point

Example:

```
sdb

└─sdb1
```

No:

```
/data
```

Meaning:

```
Filesystem exists,
but storage is not mounted.
```

Check:

```bash
mount
```

---

## Problem 3:
## Wrong Disk Selected

Before running:

```bash
fdisk
```

Always verify:

```bash
lsblk
```

Example:

```
/dev/sda

OS disk


/dev/sdb

Data disk
```

This prevents accidental data loss.

---

# ☸️ Kubernetes Connection

Kubernetes storage begins with Linux block devices.

Example:

```
Worker Node

     |

     v

/dev/sdb

     |

     v

Filesystem

     |

     v

Persistent Volume

     |

     v

Pod
```

For storage systems:

- Rook-Ceph
- Local PV
- LVM CSI

administrators first inspect disks using:

```bash
lsblk
```

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `lsblk` | List block devices |
| `lsblk -f` | Show filesystem information |
| `lsblk -p` | Show full device paths |
| `lsblk -o` | Custom output columns |
| `blkid` | Show UUID and filesystem |
| `fdisk -l` | Show partition table |
| `df -h` | Show mounted storage usage |

---

# Conclusion

`lsblk` is the first tool Linux administrators use when investigating storage.

It answers:

```
What disks exist?

How are they partitioned?

Where are they mounted?

How are they connected?
```

Before modifying storage, always:

```text
lsblk

↓

Understand the layout

↓

Make changes carefully
```