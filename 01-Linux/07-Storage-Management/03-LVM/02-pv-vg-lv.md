# 🧱 PV, VG, and LV Commands

> Inspecting and managing the three main LVM layers.

---

## Core Relationship

```text
Physical Volume
      ↓
Volume Group
      ↓
Logical Volume
```

Example:

```text
/dev/sdb1
   ↓
vg_data
   ↓
lv_database
```

---

## Physical Volumes - PV

Create a Physical Volume:

```bash
pvcreate /dev/sdb1
```

List PVs:

```bash
pvs
```

Detailed information:

```bash
pvdisplay
```

Example:

```text
PV         VG       PSize
/dev/sdb1  vg_data  500G
```

---

## Volume Groups - VG

Create a Volume Group:

```bash
vgcreate vg_data /dev/sdb1
```

List VGs:

```bash
vgs
```

Detailed information:

```bash
vgdisplay
```

Example:

```text
VG       VSize   VFree
vg_data  500G    300G
```

---

## Logical Volumes - LV

Create a Logical Volume:

```bash
lvcreate \
-L 200G \
-n lv_database \
vg_data
```

List LVs:

```bash
lvs
```

Detailed information:

```bash
lvdisplay
```

Example:

```text
LV           VG       LSize
lv_database  vg_data  200G
```

---

## Device Path

A Logical Volume can be accessed as:

```text
/dev/vg_data/lv_database
```

or:

```text
/dev/mapper/vg_data-lv_database
```

Check:

```bash
lsblk
```

---

## Add Another Disk to a VG

Suppose:

```text
/dev/sdc1
```

is a new disk.

Create PV:

```bash
pvcreate /dev/sdc1
```

Add it to the existing VG:

```bash
vgextend vg_data /dev/sdc1
```

Check:

```bash
vgs
pvs
```

---

## Remove a PV from a VG

First move allocated data if required:

```bash
pvmove /dev/sdc1
```

Remove from VG:

```bash
vgreduce vg_data /dev/sdc1
```

Remove LVM metadata:

```bash
pvremove /dev/sdc1
```

---

## Rename Objects

Rename VG:

```bash
vgrename vg_data vg_storage
```

Rename LV:

```bash
lvrename vg_storage \
lv_database \
lv_db
```

---

## Remove LVM Objects

Remove LV:

```bash
lvremove /dev/vg_data/lv_database
```

Remove VG:

```bash
vgremove vg_data
```

Remove PV:

```bash
pvremove /dev/sdb1
```

⚠️ Removing LVM objects can destroy data.

---

## Useful Inspection Commands

```bash
pvs
vgs
lvs
```

More detail:

```bash
pvdisplay
vgdisplay
lvdisplay
```

Storage hierarchy:

```bash
lsblk
```

---

## Quick Reference

| Layer | Create | View | Remove |
|---|---|---|---|
| PV | `pvcreate` | `pvs` | `pvremove` |
| VG | `vgcreate` | `vgs` | `vgremove` |
| LV | `lvcreate` | `lvs` | `lvremove` |

---

## Conclusion

The three LVM layers have distinct roles:

```text
PV = physical storage

VG = storage pool

LV = usable virtual disk
```

For day-to-day administration, remember:

```bash
pvs
vgs
lvs
```

These three commands provide a quick overview of the entire LVM layout.