# 🧱 LVM - Logical Volume Management

> Flexible storage management for Linux systems.

LVM adds an abstraction layer between physical disks and filesystems.

```mermaid
flowchart LR

    A["💽 Disk<br>/dev/sdb"] --> B["PV<br>Physical Volume"]
    B --> C["VG<br>Volume Group"]
    C --> D["LV<br>Logical Volume"]
    D --> E["📁 Filesystem"]
    E --> F["📂 Mount Point"]
```

## Why Use LVM?

LVM makes storage easier to manage by allowing administrators to:

- Combine multiple disks
- Create flexible logical volumes
- Expand storage
- Manage space without relying directly on fixed partitions
- Create snapshots

## Core Components

| Component | Meaning |
|---|---|
| PV | Physical Volume |
| VG | Volume Group |
| LV | Logical Volume |

Example:

```text
/dev/sdb1
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

## Useful Commands

```bash
pvs
vgs
lvs

pvcreate
vgcreate
lvcreate

lvextend
```

## Topics

```text
03-LVM/
├── README.md
├── concepts.md
├── pv-vg-lv.md
├── create-lvm.md
└── resize.md
```

LVM is widely used on Linux servers because it provides much more flexibility than managing fixed disk partitions directly.