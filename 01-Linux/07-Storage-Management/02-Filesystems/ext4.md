# 📁 EXT4 Filesystem

> Understanding and managing the most common Linux filesystem.

---

# 🎯 What Problem Does It Solve?

A partition alone cannot store files.

Example:

```
/dev/sdb1

500GB partition
```

The system does not yet know how to organize data.

A filesystem provides:

- File organization
- Directory structure
- Metadata management
- Permissions
- File allocation
- Data integrity

The workflow:

```
Partition

/dev/sdb1

      ↓

Filesystem

ext4

      ↓

Mount

/data

      ↓

Files
```

---

# 🧩 What Is ext4?

`ext4` (Fourth Extended Filesystem) is a popular Linux filesystem.

It is:

- Stable
- Mature
- Widely supported
- Suitable for general Linux workloads

Common uses:

- Desktop Linux
- Servers
- Virtual machines
- General application storage

---

# 📚 ext4 Features

| Feature | Description |
|-|-|
| Journaling | Protects filesystem consistency |
| Large files | Supports very large files |
| Extents | Efficient disk allocation |
| Backward compatible | Supports ext2/ext3 migration |
| Online resize | Filesystem can grow while mounted |

---

# 🧱 Filesystem Structure

Simplified view:

```
Filesystem

      |

      +-- Superblock
      |
      +-- Inodes
      |
      +-- Data Blocks
      |
      +-- Journal
```

---

# 📌 Superblock

The superblock stores filesystem information:

- Filesystem size
- Block size
- Mount information
- Filesystem state

View information:

```bash
dumpe2fs /dev/sdb1
```

---

# 📌 Inodes

An inode stores file metadata:

```
File

 |

 +-- Permissions

 +-- Owner

 +-- Size

 +-- Timestamps

 +-- Data location
```

View inode information:

```bash
ls -li
```

Example:

```
123456 file.txt
```

`123456` is the inode number.

---

# 📝 Create an ext4 Filesystem

Command:

```bash
mkfs.ext4 /dev/sdb1
```

Example:

```
Creating filesystem with 26214400 blocks
Filesystem UUID: xxxx
```

After this:

```
/dev/sdb1

        |

        v

ext4 filesystem
```

---

# 🔎 Check Filesystem Information

## Using lsblk

```bash
lsblk -f
```

Example:

```
NAME

sdb1

FSTYPE

ext4

UUID

xxxx
```

---

## Using blkid

```bash
blkid
```

Example:

```
/dev/sdb1:

TYPE="ext4"
UUID="abcd1234"
```

---

# 📂 Mount an ext4 Filesystem

Create mount point:

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
Filesystem

/dev/sdb1

Mounted on

/data
```

---

# 🔄 Persistent Mounting

Temporary mount:

```bash
mount /dev/sdb1 /data
```

After reboot:

```
Mount disappears
```

Permanent mount requires:

```text
/etc/fstab
```

Example:

```
UUID=abcd1234

/data

ext4

defaults

0 0
```

---

# 🔍 Check ext4 Filesystem

Command:

```bash
fsck
```

Example:

```bash
fsck /dev/sdb1
```

Important:

Filesystem checks should normally be performed on an unmounted filesystem.

---

# 🛠️ Repair ext4 Filesystem

Example:

```bash
fsck -y /dev/sdb1
```

Options:

| Option | Purpose |
|-|-|
| `-y` | Automatically answer yes |
| `-f` | Force check |

---

# 📈 Resize ext4 Filesystem

ext4 supports filesystem expansion.

Example:

Increase partition first:

```
Partition

500GB

      ↓

700GB
```

Then:

```bash
resize2fs /dev/sdb1
```

Check:

```bash
df -h
```

---

# 🖥️ Real Rocky Linux Example

Scenario:

A new disk:

```
/dev/sdb

100GB
```

Goal:

Create:

```
/data
```

---

## Step 1: Create partition

```bash
fdisk /dev/sdb
```

Result:

```
/dev/sdb1
```

---

## Step 2: Create filesystem

```bash
mkfs.ext4 /dev/sdb1
```

---

## Step 3: Create mount point

```bash
mkdir /data
```

---

## Step 4: Mount

```bash
mount /dev/sdb1 /data
```

---

## Step 5: Verify

```bash
df -h
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## Wrong Filesystem Type

Check:

```bash
lsblk -f
```

Example:

```
sdb1 xfs
```

but expected:

```
ext4
```

Solution:

Recreate filesystem:

```bash
mkfs.ext4 /dev/sdb1
```

⚠️ This destroys existing data.

---

## Problem 2:
## Mount Fails

Error:

```
wrong filesystem type
```

Check:

```bash
blkid /dev/sdb1
```

Install required tools if needed.

---

## Problem 3:
## Disk Full

Check:

```bash
df -h
```

Find large directories:

```bash
du -sh /*
```

---

## Problem 4:
## Filesystem Errors

Check:

```bash
fsck /dev/sdb1
```

Repair:

```bash
fsck -y /dev/sdb1
```

---

# ☸️ Kubernetes Connection

Filesystem knowledge is the foundation of container storage.

Example:

```
Linux Node

      |

      v

ext4 Filesystem

      |

      v

Persistent Volume

      |

      v

Pod

      |

      v

Application Data
```

Used with:

- Local Persistent Volumes
- Database storage
- Application data directories

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `mkfs.ext4` | Create ext4 filesystem |
| `lsblk -f` | Show filesystem information |
| `blkid` | Show UUID and type |
| `mount` | Attach filesystem |
| `df -h` | Show usage |
| `dumpe2fs` | Show filesystem details |
| `fsck` | Check filesystem |
| `resize2fs` | Resize ext filesystem |
| `ls -li` | Show inode numbers |

---

# Conclusion

A partition creates storage space, but a filesystem makes that space usable.

The Linux storage workflow is:

```
Disk

 ↓

Partition

 ↓

ext4 Filesystem

 ↓

Mount Point

 ↓

Application Data
```

Understanding ext4 provides the foundation for managing Linux storage before moving into enterprise filesystems such as XFS and LVM.