# 🛠️ mkfs - Create Filesystems

> Preparing a partition or disk with a Linux filesystem.

---

# 🎯 What Problem Does It Solve?

A partition only provides raw storage space.

Example:

```
/dev/sdb1

500GB
```

Linux cannot store files on it yet.

A filesystem organizes the storage:

```
/dev/sdb1

      ↓

Filesystem

      ↓

Directories

      ↓

Files
```

`mkfs` creates this filesystem structure.

---

# 🧩 Storage Preparation Workflow

```text
Physical Disk

      ↓

Partition

      ↓

mkfs

      ↓

Filesystem

      ↓

Mount

      ↓

Application Data
```

Example:

```
/dev/sdb

    |

    v

/dev/sdb1

    |

    v

XFS

    |

    v

/data
```

---

# 🔎 Basic Syntax

General syntax:

```bash
mkfs -t filesystem device
```

Example:

```bash
mkfs -t xfs /dev/sdb1
```

or:

```bash
mkfs.xfs /dev/sdb1
```

---

# 📚 Common Filesystem Commands

| Filesystem | Command |
|-|-|
| ext4 | `mkfs.ext4` |
| XFS | `mkfs.xfs` |
| FAT | `mkfs.vfat` |
| Btrfs | `mkfs.btrfs` |

---

# 📁 Create an EXT4 Filesystem

Command:

```bash
mkfs.ext4 /dev/sdb1
```

Example output:

```
Creating filesystem with 26214400 blocks

Filesystem UUID:
abcd-1234
```

Result:

```
/dev/sdb1

      ↓

EXT4 filesystem
```

---

# 📁 Create an XFS Filesystem

Command:

```bash
mkfs.xfs /dev/sdb1
```

Example:

```
meta-data=/dev/sdb1

data blocks=...
```

Result:

```
/dev/sdb1

      ↓

XFS filesystem
```

---

# ⚠️ Important Warning

`mkfs` destroys existing filesystem data.

Example:

```bash
mkfs.xfs /dev/sdb1
```

will erase:

```
Existing filesystem

        ↓

New empty XFS filesystem
```

Always verify:

```bash
lsblk
```

before running `mkfs`.

---

# 🔍 Check Before Creating

Show disks:

```bash
lsblk
```

Example:

```
sda     100G

sdb     500G
```

Confirm:

```
/dev/sdb
```

is the intended disk.

---

# 🔍 Check Existing Filesystem

Before formatting:

```bash
blkid
```

Example:

```
/dev/sdb1:

TYPE="ext4"
```

or:

```bash
lsblk -f
```

Example:

```
NAME

sdb1

FSTYPE

xfs
```

---

# 🧹 Remove Existing Filesystem Signatures

Sometimes a disk contains old metadata.

Example:

```
Disk previously used by:

- RAID
- LVM
- Another filesystem
```

Check:

```bash
wipefs /dev/sdb
```

Example:

```
DEVICE

sdb

TYPE

LVM2_member
```

Remove signatures:

```bash
wipefs -a /dev/sdb
```

⚠️ This removes filesystem/LVM/RAID signatures.

---

# 🏷️ Filesystem Labels

Labels provide human-readable names.

Example:

```bash
mkfs.ext4 \
-L DATA \
/dev/sdb1
```

Check:

```bash
blkid
```

Output:

```
LABEL="DATA"
```

---

# 🆔 UUIDs

Every filesystem has a UUID.

Example:

```
UUID="abcd-1234"
```

View:

```bash
blkid
```

UUIDs are commonly used in:

```text
/etc/fstab
```

Example:

```
UUID=abcd-1234 /data xfs defaults 0 0
```

---

# 🖥️ Real Linux Example

Scenario:

New disk:

```
/dev/sdb

1TB
```

Goal:

Create storage:

```
/database
```

---

## Step 1: Check disk

```bash
lsblk
```

---

## Step 2: Create partition

```bash
fdisk /dev/sdb
```

Result:

```
/dev/sdb1
```

---

## Step 3: Create filesystem

For XFS:

```bash
mkfs.xfs /dev/sdb1
```

---

## Step 4: Verify

```bash
lsblk -f
```

Output:

```
sdb1

xfs
```

---

## Step 5: Mount

```bash
mkdir /database

mount /dev/sdb1 /database
```

---

# 🛠️ Useful mkfs Options

## Show help

```bash
mkfs.xfs -h
```

---

## Create labeled filesystem

```bash
mkfs.xfs \
-L database \
/dev/sdb1
```

---

## Force overwrite

Some filesystem tools require confirmation.

Example:

```bash
mkfs.xfs -f /dev/sdb1
```

⚠️ Use carefully.

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## Device Is Busy

Example:

```
/dev/sdb1 is mounted
```

Check:

```bash
mount | grep sdb1
```

Unmount:

```bash
umount /dev/sdb1
```

Then retry.

---

## Problem 2:
## Wrong Filesystem Created

Check:

```bash
lsblk -f
```

Example:

```
sdb1 ext4
```

Need:

```
xfs
```

Recreate:

```bash
mkfs.xfs /dev/sdb1
```

---

## Problem 3:
## Old LVM Metadata Remains

Check:

```bash
wipefs /dev/sdb
```

Remove:

```bash
wipefs -a /dev/sdb
```

---

# ☸️ Kubernetes Connection

Container storage ultimately depends on filesystems.

Example:

```
Node Disk

      ↓

Partition

      ↓

mkfs.xfs

      ↓

Filesystem

      ↓

Persistent Volume

      ↓

Pod
```

Common in:

- Local PV
- Database workloads
- Container runtimes
- Storage preparation

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `mkfs -t TYPE DEVICE` | Create filesystem |
| `mkfs.ext4` | Create ext4 filesystem |
| `mkfs.xfs` | Create XFS filesystem |
| `blkid` | Show filesystem UUID/type |
| `lsblk -f` | Show filesystem information |
| `wipefs` | Show filesystem signatures |
| `wipefs -a` | Remove signatures |

---

# Conclusion

`mkfs` transforms an empty partition into usable storage.

The Linux storage workflow:

```
Disk

 ↓

Partition

 ↓

mkfs

 ↓

Filesystem

 ↓

Mount

 ↓

Application
```

Before running `mkfs`, always remember:

```
Check the device.
Confirm the target.
Formatting destroys existing data.
```