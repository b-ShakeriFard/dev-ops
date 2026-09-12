# 🔗 Hard Links and Symbolic Links

> Understanding how Linux can reference the same data through different filenames.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Hard Links](#hard-links)
- [Symbolic Links](#symbolic-links)
- [Key Differences](#key-differences)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ln TARGET LINK` | Create hard link |
| `ln -s TARGET LINK` | Create symbolic link |
| `ls -li` | Show inode numbers |
| `readlink LINK` | Show symlink target |
| `readlink -f LINK` | Show resolved target path |

---

## Overview

Linux supports two main link types:

```text
Hard Link
Symbolic Link
```

Both provide another way to access data, but they work differently.

---

## Hard Links

A hard link points to the same inode as the original file.

Example:

```bash
ln file.txt hardlink.txt
```

Check:

```bash
ls -li
```

Example:

```text
12345 file.txt
12345 hardlink.txt
```

Both names reference the same underlying data.

Deleting one name does not remove the data while another hard link still exists.

---

## Symbolic Links

A symbolic link points to another pathname.

Create:

```bash
ln -s /opt/app/config.yml config.yml
```

Check:

```bash
ls -l
```

Example:

```text
config.yml -> /opt/app/config.yml
```

Inspect target:

```bash
readlink config.yml
```

---

## Key Differences

| Feature | Hard Link | Symbolic Link |
|---|---|---|
| Same inode | ✅ | ❌ |
| Can cross filesystems | ❌ | ✅ |
| Can link directories normally | ❌ | ✅ |
| Breaks if target is deleted | ❌ | ✅ |
| Created with | `ln` | `ln -s` |

---

## Practical Examples

Create test file:

```bash
echo "hello" > file.txt
```

Create hard link:

```bash
ln file.txt file-hard
```

Create symbolic link:

```bash
ln -s file.txt file-soft
```

Inspect:

```bash
ls -li
```

Delete original:

```bash
rm file.txt
```

Result:

```text
file-hard → still works
file-soft → broken link
```

---

## Common Pitfalls

### Relative Symlink Targets

Example:

```bash
ln -s ../config/app.conf app.conf
```

The target is interpreted relative to the symlink's location.

Check:

```bash
readlink -f app.conf
```

---

### Broken Symlink

Find broken links:

```bash
find . -xtype l
```

---

## Related Topics

- `file-types.md`
- `absolute-vs-relative-paths.md`
- `permissions-basics.md`

---

## Conclusion

The key distinction is:

```text
Hard Link
→ another name for the same inode

Symbolic Link
→ a pointer to another path
```

For everyday administration, symbolic links are generally more flexible and easier to recognize.