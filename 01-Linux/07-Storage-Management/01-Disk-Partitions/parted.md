# 🛠️ parted - Advanced Partition Management

> Creating and managing disk partitions using GNU Parted.

---

# 🎯 What Problem Does It Solve?

`parted` manages disk partitions, especially for:

- Large disks
- GPT partition tables
- Modern servers
- Automated installations

Example:

```
Enterprise Server

/dev/sdb

8 TB Disk

        |

        v

GPT Partition Table

        |

        v

Multiple Partitions
```

---

# 🧩 fdisk vs parted

| Tool | Best For |
|-|-|
| `fdisk` | Traditional partition management |
| `parted` | GPT, large disks, automation |

Both tools create:

```
Disk

 |

Partition

 |

Filesystem

 |

Mount Point
```

---

# 🔎 Identify Disks

Before using `parted`:

```bash
lsblk
```

Example:

```
NAME   SIZE TYPE

sda    100G disk

sdb      8T disk
```

Target:

```text
/dev/sdb
```

---

# 📋 View Partition Information

Command:

```bash
sudo parted -l
```

Example:

```
Model: VMware Virtual disk

Disk /dev/sdb: 8TB

Partition Table: gpt
```

---

# 🚀 Open parted Interactive Mode

Syntax:

```bash
sudo parted /dev/sdb
```

Prompt:

```
(parted)
```

---

# 📚 Common parted Commands

Inside `parted`:

| Command | Purpose |
|-|-|
| `print` | Show partition table |
| `mklabel` | Create partition table |
| `mkpart` | Create partition |
| `rm` | Remove partition |
| `resizepart` | Resize partition |
| `quit` | Exit |

---

# 👀 View Current Layout

Inside parted:

```text
print
```

Example:

```
Number  Start   End     Size

1       1MB     500GB   500GB
```

---

# 🧱 Create GPT Partition Table

Example:

```bash
sudo parted /dev/sdb
```

Inside:

```
mklabel gpt
```

Confirm:

```
Yes
```

⚠️ This removes existing partition information.

---

# ➕ Create a Partition

Example:

Create a partition using the entire disk:

```
mkpart primary xfs 0% 100%
```

Result:

```
/dev/sdb1
```

Verify:

```bash
lsblk
```

Output:

```
sdb

└── sdb1
```

---

# 🖥️ Real Example

Scenario:

A database server receives:

```
/dev/sdb

4 TB
```

Goal:

Create:

```
/data
```

---

## Step 1: Inspect disk

```bash
lsblk
```

---

## Step 2: Create GPT table

```bash
parted /dev/sdb
```

Inside:

```
mklabel gpt
```

---

## Step 3: Create partition

```
mkpart primary xfs 0% 100%
```

---

## Step 4: Verify

```bash
lsblk
```

Result:

```
sdb

└── sdb1
```

---

## Step 5: Create filesystem

```bash
mkfs.xfs /dev/sdb1
```

---

## Step 6: Mount

```bash
mkdir /data

mount /dev/sdb1 /data
```

---

# 🔢 Creating Multiple Partitions

Example:

Disk:

```
/dev/sdb

1TB
```

Create:

```
sdb1 → 500GB

sdb2 → 500GB
```

Commands:

```bash
parted /dev/sdb
```

Inside:

```
mklabel gpt

mkpart primary 0% 50%

mkpart primary 50% 100%
```

Verify:

```bash
print
```

---

# 📝 Non-Interactive Mode

Useful for scripts.

Example:

Create GPT:

```bash
sudo parted \
/dev/sdb \
mklabel gpt
```

Create partition:

```bash
sudo parted \
/dev/sdb \
mkpart primary 0% 100%
```

Useful in:

- Kickstart
- Ansible
- Automated provisioning

---

# 🔄 Resize a Partition

Example:

```bash
parted /dev/sdb
```

Show partition:

```
print
```

Resize:

```
resizepart 1 100%
```

Important:

`parted` changes the partition boundary.

The filesystem may also need expansion.

Example:

XFS:

```bash
xfs_growfs /mountpoint
```

ext4:

```bash
resize2fs /dev/device
```

---

# 🧱 GPT vs MBR

## MBR

Older:

```
Maximum disk size:

~2 TB
```

Limit:

```
4 primary partitions
```

---

## GPT

Modern:

```
Very large disks

Many partitions

UEFI support
```

Common today:

```
Enterprise Linux

Storage arrays

Cloud environments
```

---

# 🚨 Troubleshooting Scenarios

## Problem 1:
## Disk Uses Wrong Partition Table

Check:

```bash
parted -l
```

Example:

```
Partition Table: msdos
```

Need:

```
gpt
```

Solution:

Create GPT:

```bash
mklabel gpt
```

⚠️ Destructive operation.

---

## Problem 2:
## Partition Created But Missing

Reload:

```bash
partprobe
```

Verify:

```bash
lsblk
```

---

## Problem 3:
## Partition Expanded But Filesystem Did Not Grow

Check:

```bash
df -h
```

Partition:

```bash
lsblk
```

Filesystem expansion:

XFS:

```bash
xfs_growfs /data
```

ext4:

```bash
resize2fs /dev/sdb1
```

---

# ☸️ Kubernetes Connection

Modern storage platforms frequently use GPT-partitioned disks.

Examples:

```
Worker Node

      |

      v

/dev/sdb

      |

      v

GPT Partition

      |

      v

Storage Backend

      |

      v

Persistent Volume
```

Used with:

- Local PV
- LVM CSI
- Ceph preparation
- Database storage

---

# 📚 Quick Reference

| Command | Purpose |
|-|-|
| `parted -l` | List partition information |
| `parted /dev/sdX` | Open disk |
| `print` | Show layout |
| `mklabel gpt` | Create GPT table |
| `mkpart` | Create partition |
| `rm` | Delete partition |
| `resizepart` | Resize partition |
| `quit` | Exit |

---

# Conclusion

`parted` is the modern Linux partitioning tool for enterprise environments.

The storage preparation workflow becomes:

```text
Identify Disk

      ↓

Choose Partition Tool

      ↓

Create Partition

      ↓

Create Filesystem

      ↓

Mount Storage
```

Use:

```text
lsblk

↓

fdisk / parted

↓

mkfs

↓

mount
```

as the foundation for Linux storage administration.