# 🔪 fdisk - Disk Partition Management

> Creating and managing disk partitions in Linux.

---

# 🎯 What Problem Does It Solve?

A raw disk is not usually ready for storing data.

Example:

```
New Disk:

/dev/sdb

500GB
```

Before use:

```
/dev/sdb
```

After partitioning:

```
/dev/sdb

 |

 +-- /dev/sdb1

 +-- /dev/sdb2
```

`fdisk` creates and manages these partitions.

---

# 🧩 Storage Workflow

```text
Physical Disk

      |

      v

Partition Table

      |

      v

Partition

      |

      v

Filesystem

      |

      v

Mount Point
```

---

# 🔎 Identify the Disk

Before using `fdisk`:

```bash
lsblk
```

Example:

```
NAME   SIZE TYPE

sda    100G disk

sdb    500G disk
```

We want:

```text
/dev/sdb
```

---

# 📋 View Existing Partition Tables

List all disks:

```bash
sudo fdisk -l
```

Example:

```
Disk /dev/sdb: 500 GiB

Disklabel type: gpt
```

---

# 🚀 Open a Disk with fdisk

Syntax:

```bash
sudo fdisk /dev/<disk>
```

Example:

```bash
sudo fdisk /dev/sdb
```

You enter the interactive menu:

```
Command (m for help):
```

---

# 📚 Common fdisk Commands

Inside `fdisk`:

| Command | Purpose |
|-|-|
| `p` | Print partition table |
| `n` | Create new partition |
| `d` | Delete partition |
| `t` | Change partition type |
| `l` | List partition types |
| `w` | Write changes |
| `q` | Quit without saving |
| `m` | Show help |

---

# 👀 View Current Layout

Inside fdisk:

```text
p
```

Example:

```
Device       Size

/dev/sdb1    100G
/dev/sdb2    400G
```

---

# ➕ Create a New Partition

Open disk:

```bash
sudo fdisk /dev/sdb
```

Create partition:

```
n
```

Choose:

```
Primary partition
```

Select partition number:

```
1
```

Accept default sectors:

```
Enter
```

Write changes:

```
w
```

---

# 🔄 Reload Partition Table

After modifying partitions:

```bash
sudo partprobe
```

or:

```bash
sudo reboot
```

Verify:

```bash
lsblk
```

Example:

```
sdb

└── sdb1
```

---

# 🖥️ Real Example

Scenario:

A server receives:

```
/dev/sdb

1 TB
```

Goal:

Create:

```
/data
```

Workflow:

---

## Step 1: Identify disk

```bash
lsblk
```

Result:

```
sdb  1T
```

---

## Step 2: Partition

```bash
sudo fdisk /dev/sdb
```

Inside:

```
n
1
Enter
Enter
w
```

---

## Step 3: Verify

```bash
lsblk
```

Result:

```
sdb

└── sdb1
```

---

## Step 4: Create filesystem

Example:

```bash
mkfs.xfs /dev/sdb1
```

---

## Step 5: Mount

```bash
mkdir /data

mount /dev/sdb1 /data
```

---

# 🧱 MBR vs GPT

`fdisk` supports both partition styles.

## MBR

Older partition table:

- Maximum 4 primary partitions
- ~2 TB disk limitation

---

## GPT

Modern standard:

- Supports very large disks
- Many partitions
- Used with UEFI systems

Check:

```bash
sudo fdisk -l
```

Example:

```
Disklabel type: gpt
```

---

# 🔄 Create GPT Partition Table

Inside fdisk:

Show help:

```
m
```

Create GPT:

```
g
```

Create partition:

```
n
```

Save:

```
w
```

⚠️ This destroys existing partition information.

---

# 🗑️ Delete a Partition

Open disk:

```bash
sudo fdisk /dev/sdb
```

Delete:

```
d
```

Select partition:

```
1
```

Write:

```
w
```

---

# 🏷️ Change Partition Type

Show types:

```
l
```

Change type:

```
t
```

Example:

Linux filesystem:

```
83
```

Linux LVM:

```
8e
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## New Disk Does Not Appear

Check:

```bash
lsblk
```

Then:

```bash
dmesg | tail
```

Possible causes:

- Disk not attached
- VM disk not added
- Storage controller issue

---

## Problem 2:
## Partition Created But Not Visible

Reload:

```bash
sudo partprobe
```

Verify:

```bash
lsblk
```

---

## Problem 3:
## Wrong Disk Selected

Before:

```bash
fdisk /dev/sdX
```

Always check:

```bash
lsblk
```

Example:

```
/dev/sda

Operating System


/dev/sdb

New Data Disk
```

---

# ☸️ Kubernetes Connection

Linux partitioning is the foundation of storage systems.

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

Used in:

- Local Persistent Volumes
- LVM CSI
- Ceph preparation
- Database storage

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `fdisk -l` | List partitions |
| `fdisk /dev/sdb` | Open disk |
| `p` | Print table |
| `n` | Create partition |
| `d` | Delete partition |
| `t` | Change partition type |
| `w` | Save changes |
| `q` | Exit without saving |
| `partprobe` | Reload partition table |
| `lsblk` | Verify result |

---

# Conclusion

`fdisk` is the classic Linux partition management tool.

The administrator workflow is:

```text
Identify Disk

      ↓

Create Partition

      ↓

Create Filesystem

      ↓

Mount Storage
```

Always remember:

```text
Partitioning is destructive.

Verify the disk before writing changes.
```