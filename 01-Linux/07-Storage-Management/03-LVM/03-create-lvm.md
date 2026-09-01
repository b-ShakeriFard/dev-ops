# 🏗️ Creating LVM Storage

> Creating a complete LVM stack from disk to mount point.

---

## Goal

Create:

```text
/dev/sdb
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

## 1. Identify the Disk

Check available disks:

```bash
lsblk
```

Example:

```text
sda    100G
sdb    500G
```

Target:

```text
/dev/sdb
```

---

## 2. Create a Partition

Open the disk:

```bash
fdisk /dev/sdb
```

Create a new partition:

```text
n
```

Save:

```text
w
```

Verify:

```bash
lsblk
```

Result:

```text
/dev/sdb1
```

---

## 3. Create the Physical Volume

```bash
pvcreate /dev/sdb1
```

Verify:

```bash
pvs
```

---

## 4. Create the Volume Group

```bash
vgcreate vg_data /dev/sdb1
```

Verify:

```bash
vgs
```

---

## 5. Create the Logical Volume

Create a 200 GB LV:

```bash
lvcreate \
-L 200G \
-n lv_database \
vg_data
```

Verify:

```bash
lvs
```

Logical Volume:

```text
/dev/vg_data/lv_database
```

---

## 6. Create a Filesystem

Create XFS:

```bash
mkfs.xfs \
/dev/vg_data/lv_database
```

Verify:

```bash
lsblk -f
```

---

## 7. Create the Mount Point

```bash
mkdir /database
```

---

## 8. Mount the Logical Volume

```bash
mount \
/dev/vg_data/lv_database \
/database
```

Verify:

```bash
df -h /database
```

---

## 9. Make the Mount Persistent

Find the filesystem UUID:

```bash
blkid \
/dev/vg_data/lv_database
```

Add it to:

```text
/etc/fstab
```

Example:

```text
UUID=xxxx-xxxx  /database  xfs  defaults  0 0
```

Test:

```bash
mount -a
```

---

## Use All Free Space

Instead of specifying a fixed size:

```bash
lvcreate \
-l 100%FREE \
-n lv_database \
vg_data
```

This allocates all remaining space in the VG.

---

## Verify the Entire LVM Stack

```bash
pvs
vgs
lvs
lsblk
df -h
```

Example structure:

```text
sdb
└─sdb1
   └─vg_data-lv_database
      └─ /database
```

---

## Quick Workflow

```bash
lsblk

pvcreate /dev/sdb1

vgcreate vg_data /dev/sdb1

lvcreate -L 200G \
-n lv_database \
vg_data

mkfs.xfs \
/dev/vg_data/lv_database

mkdir /database

mount \
/dev/vg_data/lv_database \
/database
```

---

## Common Mistakes

### Wrong Disk Selected

Always verify first:

```bash
lsblk
```

---

### No Free Space in VG

Check:

```bash
vgs
```

Look at:

```text
VFree
```

---

### Filesystem Created but Not Mounted

Check:

```bash
lsblk -f
```

Then:

```bash
mount \
/dev/vg_data/lv_database \
/database
```

---

## Conclusion

The complete LVM creation process is:

```text
Disk
 ↓
Partition
 ↓
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

The commands to remember are:

```bash
pvcreate
vgcreate
lvcreate
mkfs
mount
```