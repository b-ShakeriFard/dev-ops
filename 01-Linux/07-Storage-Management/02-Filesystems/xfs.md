# 📁 XFS Filesystem

> Understanding and managing the default enterprise Linux filesystem.

---

# 🎯 What Problem Does It Solve?

Enterprise systems need filesystems that can handle:

- Large disks
- Large files
- High I/O workloads
- Database storage
- Application data
- Long-running servers

XFS provides a scalable filesystem designed for enterprise workloads.

---

# 🧩 What Is XFS?

XFS is a high-performance journaling filesystem originally developed by Silicon Graphics (SGI).

Today it is widely used in:

- Red Hat Enterprise Linux
- Rocky Linux
- AlmaLinux
- Enterprise servers

Default filesystem:

```text
RHEL 7+

        ↓

XFS
```

---

# 📚 XFS Features

| Feature | Description |
|-|-|
| Journaling | Protects filesystem consistency |
| High scalability | Designed for large storage |
| Large files | Supports very large files |
| Parallel I/O | Good performance under load |
| Online expansion | Grow filesystem while mounted |

---

# 🆚 XFS vs EXT4

| Feature | XFS | EXT4 |
|-|-|-|
| Enterprise Linux default | ✅ | ❌ |
| Journaling | ✅ | ✅ |
| Online growth | ✅ | ✅ |
| Shrinking filesystem | ❌ | ✅ |
| Large storage | Excellent | Good |
| Common in RHEL | Yes | Less common |

Important:

```
XFS can grow.

XFS cannot shrink.
```

---

# 🧱 XFS Structure

Simplified:

```
XFS Filesystem

        |

        +-- Allocation Groups

        |

        +-- Metadata

        |

        +-- Data Blocks

        |

        +-- Journal
```

XFS divides storage into allocation groups to improve parallel operations.

---

# 🔎 Check XFS Information

Show filesystem type:

```bash
lsblk -f
```

Example:

```
NAME   FSTYPE

sda1   xfs
```

---

Show detailed information:

```bash
xfs_info /mountpoint
```

Example:

```bash
xfs_info /
```

Output:

```
meta-data=/dev/sda2

isize=512

agcount=4
```

---

# 📝 Create an XFS Filesystem

Command:

```bash
mkfs.xfs /dev/sdb1
```

Example:

```bash
sudo mkfs.xfs /dev/sdb1
```

Result:

```
meta-data=/dev/sdb1
data blocks=...
```

Now:

```
/dev/sdb1

        ↓

XFS filesystem
```

---

# 📂 Mount an XFS Filesystem

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

Temporary:

```bash
mount /dev/sdb1 /data
```

After reboot:

```
Mount disappears
```

Permanent configuration:

```text
/etc/fstab
```

Example:

```
UUID=xxxx

/data

xfs

defaults

0 0
```

Find UUID:

```bash
blkid
```

---

# 📈 Grow an XFS Filesystem

XFS supports online expansion.

Example:

Before:

```
/data

500GB
```

Disk expanded:

```
1TB
```

Grow filesystem:

```bash
xfs_growfs /data
```

Verify:

```bash
df -h
```

---

# ⚠️ XFS Cannot Shrink

Example:

```
500GB XFS

        ↓

Want

300GB
```

Not supported.

Solution:

1. Backup data
2. Create smaller filesystem
3. Restore data

Workflow:

```
Backup

 ↓

Create new filesystem

 ↓

Copy data

 ↓

Mount new storage
```

---

# 🔍 Check XFS Filesystem

Command:

```bash
xfs_info /data
```

Example:

```bash
xfs_info /
```

---

# 🛠️ Repair XFS Filesystem

Unlike ext4:

```bash
fsck
```

is not used for XFS.

Use:

```bash
xfs_repair
```

Example:

```bash
xfs_repair /dev/sdb1
```

Important:

Filesystem should normally be unmounted.

---

# 🖥️ Real Rocky Linux Example

Scenario:

A Rocky Linux server receives:

```
/dev/sdb

2TB
```

Goal:

Create:

```
/database
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

## Step 2: Create XFS filesystem

```bash
mkfs.xfs /dev/sdb1
```

---

## Step 3: Create mount point

```bash
mkdir /database
```

---

## Step 4: Mount

```bash
mount /dev/sdb1 /database
```

---

## Step 5: Verify

```bash
df -h
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## Mount Fails

Check filesystem:

```bash
blkid /dev/sdb1
```

Example:

```
TYPE="xfs"
```

Mount explicitly:

```bash
mount -t xfs /dev/sdb1 /data
```

---

## Problem 2:
## Filesystem Full

Check:

```bash
df -h
```

Find large directories:

```bash
du -sh /*
```

---

## Problem 3:
## XFS Needs Expansion

Check:

```bash
df -h
```

Grow:

```bash
xfs_growfs /mountpoint
```

---

## Problem 4:
## Filesystem Corruption

Check:

```bash
xfs_repair /dev/device
```

For severe cases:

```bash
xfs_repair -L /dev/device
```

⚠️ `-L` clears the journal and may cause data loss.

Use carefully.

---

# ☸️ Kubernetes Connection

XFS is very common in container platforms.

Example:

```
Worker Node

      |

      v

XFS Filesystem

      |

      v

Container Runtime

      |

      v

Persistent Data
```

Used with:

- OpenShift nodes
- RHEL Kubernetes nodes
- Local storage
- Database workloads

---

# 🏢 Enterprise Examples

Common XFS mounts:

```
/var/lib/containers

/var/lib/kubelet

/data

/database

/logs
```

Container platforms often store:

```
Images

Containers

Volumes

Logs
```

on XFS-backed storage.

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `mkfs.xfs` | Create XFS filesystem |
| `xfs_info` | Show XFS details |
| `xfs_growfs` | Expand XFS filesystem |
| `xfs_repair` | Repair XFS |
| `lsblk -f` | Show filesystem type |
| `blkid` | Show UUID |
| `df -h` | Show usage |

---

# Conclusion

XFS is the standard enterprise filesystem in modern Linux environments.

The workflow:

```
Disk

 ↓

Partition

 ↓

XFS Filesystem

 ↓

Mount Point

 ↓

Application Data
```

For Enterprise Linux administrators, understanding XFS is essential because it powers many production systems, including Kubernetes and OpenShift environments.