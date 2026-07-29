# 💾 Linux Storage Management

> Understanding disks, partitions, filesystems, mounts, and logical storage management in Linux.



Every Linux system needs storage to:

- Store the operating system
- Save application data
- Host databases
- Store logs
- Provide persistent volumes

The Linux storage stack:


---

## 🎯 What Problem Does Storage Management Solve?

Linux administrators need to answer:

- How much disk space is available?
- Which disk contains my data?
- How should disks be partitioned?
- Which filesystem should I use?
- How can I expand storage without downtime?
- How do I troubleshoot disk problems?

---

# 🧩 Storage Components

## Physical Disk

The actual storage device:


View disks:

```bash
lsblk
```

---

## Partitions

A disk can be divided into smaller sections.


Tools:

```bash
fdisk
```

```bash
parted
```

---

## Filesystem

A filesystem organizes data on a disk.

Common Linux filesystems:

| Filesystem | Usage |
|-|-|
| ext4 | General Linux systems |
| XFS | Enterprise Linux default |
| tmpfs | Memory-based filesystem |

Examples:

```bash
mkfs.ext4
```

```bash
mkfs.xfs
```

---

## Mount Points

Linux accesses storage through directories.


View mounts:

```bash
mount
```

or:

```bash
df -h
```

---

## 🔥 LVM (Logical Volume Management)

LVM adds flexibility between disks and filesystems.

Traditional storage:

```
Disk

 |

Partition

 |

Filesystem
```

LVM:

```
Disk

 |

Physical Volume (PV)

 |

Volume Group (VG)

 |

Logical Volume (LV)

 |

Filesystem
```

Benefits:

- Resize storage
- Combine disks
- Create flexible layouts
- Take snapshots

---

# 🛠️ Essential Storage Commands

| Command | Purpose |
|-|-|
| `lsblk` | List disks and partitions |
| `fdisk` | Manage partitions |
| `parted` | Advanced partition management |
| `blkid` | Show filesystem information |
| `mkfs` | Create filesystem |
| `mount` | Attach filesystem |
| `umount` | Detach filesystem |
| `df -h` | Show filesystem usage |
| `du -sh` | Show directory usage |
| `lvs` | Show logical volumes |
| `vgs` | Show volume groups |
| `pvs` | Show physical volumes |


---

# 🖥️ Real Linux Example

A server administrator receives a new disk:

```
/dev/sdb

1 TB
```

The workflow:

## 1. Detect disk

```bash
lsblk
```

---

## 2. Create partition

```bash
fdisk /dev/sdb
```

---

## 3. Create filesystem

Example:

```bash
mkfs.xfs /dev/sdb1
```

---

## 4. Create mount point

```bash
mkdir /data
```

---

## 5. Mount storage

```bash
mount /dev/sdb1 /data
```

---

## 6. Make persistent

Configure:

```text
/etc/fstab
```

---

# ☸️ DevOps Connection

Linux storage concepts are the foundation of container storage.

Traditional Linux:

```
Disk

 |

Filesystem

 |

Application
```

Kubernetes:

```
Storage Device

 |

Persistent Volume

 |

Persistent Volume Claim

 |

Pod

 |

Application
```

Understanding Linux storage makes Kubernetes storage much easier.

---

# 📂 Chapter Structure

```
07-Storage/

├── README.md

├── 01-Disks-Partitions/
│   ├── fdisk.md
│   ├── parted.md
│   └── lsblk.md

├── 02-Filesystems/
│   ├── ext4.md
│   ├── xfs.md
│   ├── mkfs.md
│   └── mount.md

├── 03-LVM/
│   ├── concepts.md
│   ├── pv-vg-lv.md
│   ├── create-lvm.md
│   └── resize.md

├── 04-Mounting/
│   ├── fstab.md
│   ├── automount.md
│   └── troubleshooting.md

└── 05-Troubleshooting/
    ├── disk-space.md
    ├── filesystem-errors.md
    └── performance.md
```

---

# Conclusion

Storage management is one of the core responsibilities of a Linux administrator.

The essential path is:

```text
Disk

 ↓

Partition

 ↓

Filesystem

 ↓

Mount

 ↓

LVM

 ↓

Application Data
```

Mastering Linux storage provides the foundation for databases, virtualization, containers, Kubernetes, and enterprise infrastructure.
