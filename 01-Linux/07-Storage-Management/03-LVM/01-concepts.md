# 🧱 LVM Concepts

> Understanding the core building blocks of Logical Volume Management.

---

## What Is LVM?

LVM adds a flexible storage layer between physical disks and filesystems.

Traditional layout:

```text
Disk
 ↓
Partition
 ↓
Filesystem
 ↓
Mount Point
```

With LVM:

```text
Disk
 ↓
Physical Volume
 ↓
Volume Group
 ↓
Logical Volume
 ↓
Filesystem
 ↓
Mount Point
```

---

## Core Components

### Physical Volume - PV

A disk or partition prepared for use by LVM.

Example:

```text
/dev/sdb1
```

Create:

```bash
pvcreate /dev/sdb1
```

View:

```bash
pvs
```

---

### Volume Group - VG

A storage pool created from one or more Physical Volumes.

Example:

```text
/dev/sdb1
/dev/sdc1
     ↓
   vg_data
```

Create:

```bash
vgcreate vg_data /dev/sdb1 /dev/sdc1
```

View:

```bash
vgs
```

---

### Logical Volume - LV

A virtual storage volume created from a Volume Group.

Example:

```text
vg_data
   ↓
lv_database
```

Create:

```bash
lvcreate -L 100G -n lv_database vg_data
```

View:

```bash
lvs
```

---

## Full Example

```text
/dev/sdb1
/dev/sdc1
     ↓
     PV
     ↓
   vg_data
     ↓
lv_database
     ↓
    XFS
     ↓
 /database
```

---

## Why LVM Is Useful

LVM makes it easier to:

- Combine multiple disks
- Expand logical volumes
- Manage storage pools
- Separate application storage
- Create snapshots

---

## LVM vs Traditional Partitions

Traditional:

```text
/dev/sdb1 → 100 GB
```

If more space is needed, changing the layout can be difficult.

LVM:

```text
VG = 500 GB

├── lv_database = 200 GB
├── lv_logs     = 100 GB
└── Free        = 200 GB
```

Free space can later be assigned where needed.

---

## Important Commands

| Command | Purpose |
|---|---|
| `pvs` | Show Physical Volumes |
| `vgs` | Show Volume Groups |
| `lvs` | Show Logical Volumes |
| `pvcreate` | Create PV |
| `vgcreate` | Create VG |
| `lvcreate` | Create LV |
| `lvextend` | Extend LV |

---

## Key Mental Model

Think of LVM like this:

```text
PV = Storage Devices

VG = Storage Pool

LV = Virtual Disk
```

The Logical Volume is then formatted and mounted like a normal disk:

```bash
mkfs.xfs /dev/vg_data/lv_database
mount /dev/vg_data/lv_database /database
```

---

## Conclusion

LVM separates physical storage from the storage presented to applications.

The essential relationship is:

```text
PV
 ↓
VG
 ↓
LV
 ↓
Filesystem
 ↓
Mount Point
```

Once this model is clear, most LVM commands become much easier to understand.