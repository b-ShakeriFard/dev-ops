# 📈 Resizing LVM Storage

> Extending Logical Volumes and filesystems safely.

---

## Why Resize LVM?

One of LVM’s biggest advantages is flexible expansion.

Example:

```text
lv_database = 100G

        ↓

Application needs more space

        ↓

lv_database = 200G
```

---

## Check Current Space

Check Logical Volumes:

```bash
lvs
```

Check Volume Group free space:

```bash
vgs
```

Example:

```text
VG       VSize   VFree
vg_data  500G    200G
```

---

## Extend a Logical Volume

Add 50 GB:

```bash
lvextend \
-L +50G \
/dev/vg_data/lv_database
```

Verify:

```bash
lvs
```

---

## Use All Remaining VG Space

```bash
lvextend \
-l +100%FREE \
/dev/vg_data/lv_database
```

This allocates all remaining free extents in the VG.

---

## Grow the Filesystem

Extending the LV does **not always automatically grow the filesystem**.

For XFS:

```bash
xfs_growfs /database
```

For ext4:

```bash
resize2fs \
/dev/vg_data/lv_database
```

Verify:

```bash
df -h /database
```

---

## Resize LV and Filesystem Together

A very useful option:

```bash
lvextend \
-r \
-L +50G \
/dev/vg_data/lv_database
```

`-r` tells LVM to resize the filesystem as well.

---

## Full Example

Before:

```text
VG free space: 200G
LV size:       100G
```

Extend:

```bash
lvextend \
-r \
-L +100G \
/dev/vg_data/lv_database
```

After:

```text
LV size: 200G
```

Verify:

```bash
lvs
df -h /database
```

---

## Add a New Disk to Gain More Space

Suppose the VG has no free space.

New disk:

```text
/dev/sdc1
```

Create PV:

```bash
pvcreate /dev/sdc1
```

Add to VG:

```bash
vgextend \
vg_data \
/dev/sdc1
```

Check:

```bash
vgs
```

Then extend the LV:

```bash
lvextend \
-r \
-L +200G \
/dev/vg_data/lv_database
```

---

## Complete Expansion Flow

```text
New Disk
   ↓
pvcreate
   ↓
vgextend
   ↓
lvextend
   ↓
Filesystem Grow
   ↓
More Application Space
```

---

## XFS Example

Check:

```bash
df -h /database
```

Extend LV:

```bash
lvextend \
-L +50G \
/dev/vg_data/lv_database
```

Grow XFS:

```bash
xfs_growfs /database
```

---

## ext4 Example

Extend LV:

```bash
lvextend \
-L +50G \
/dev/vg_data/lv_database
```

Grow filesystem:

```bash
resize2fs \
/dev/vg_data/lv_database
```

---

## Shrinking Logical Volumes

Shrinking is more dangerous than extending.

Important:

```text
XFS cannot shrink.
```

ext4 can shrink, but the filesystem must normally be reduced **before** reducing the LV.

Never run:

```bash
lvreduce
```

without verifying filesystem size first.

Incorrect shrinking can destroy data.

---

## Useful Commands

| Command | Purpose |
|---|---|
| `lvs` | Check LV size |
| `vgs` | Check VG free space |
| `lvextend -L +50G` | Add 50 GB |
| `lvextend -l +100%FREE` | Use all free space |
| `lvextend -r` | Resize LV and filesystem |
| `xfs_growfs` | Grow XFS |
| `resize2fs` | Resize ext4 |
| `vgextend` | Add storage to VG |

---

## Troubleshooting

### No Free Space in VG

Check:

```bash
vgs
```

Add another PV:

```bash
pvcreate /dev/sdc1
vgextend vg_data /dev/sdc1
```

---

### LV Grew but `df -h` Did Not

Check:

```bash
lvs
df -h
```

The LV may be larger while the filesystem is still unchanged.

Grow it:

XFS:

```bash
xfs_growfs /database
```

ext4:

```bash
resize2fs /dev/vg_data/lv_database
```

---

## Conclusion

LVM expansion normally follows:

```text
Check VG Space
      ↓
Extend LV
      ↓
Grow Filesystem
      ↓
Verify
```

The convenient command:

```bash
lvextend -r
```

can perform the LV and filesystem expansion together.