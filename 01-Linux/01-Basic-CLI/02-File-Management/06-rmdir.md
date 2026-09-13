# 📂 rmdir

> Removing empty directories safely from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Removing Parent Directories](#removing-parent-directories)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `rmdir DIR` | Remove empty directory |
| `rmdir DIR1 DIR2` | Remove multiple empty directories |
| `rmdir -p PATH` | Remove directory and empty parents |
| `rmdir -v DIR` | Verbose output |

---

## Overview

`rmdir` means:

```text
remove directory
```

It removes **empty directories only**.

Example:

```bash
rmdir old-project
```

If the directory contains files or subdirectories, the command fails.

---

## Basic Usage

Remove one empty directory:

```bash
rmdir test
```

Remove several:

```bash
rmdir dir1 dir2 dir3
```

Check first:

```bash
ls -la test
```

---

## Removing Parent Directories

Suppose you have:

```text
project/config/nginx
```

and all directories are empty.

You can remove them with:

```bash
rmdir -p project/config/nginx
```

This attempts to remove:

```text
nginx
config
project
```

in that order.

It stops if one of the parent directories is not empty.

---

## Practical Examples

Create an empty directory:

```bash
mkdir test
```

Remove it:

```bash
rmdir test
```

Verbose removal:

```bash
rmdir -v test
```

Remove nested empty directories:

```bash
rmdir -p demo/logs/archive
```

---

## Common Pitfalls

### Directory Not Empty

Example:

```text
rmdir: failed to remove 'project': Directory not empty
```

Check:

```bash
ls -la project
```

If you really intend to remove everything inside:

```bash
rm -r project
```

Use that carefully.

### Hidden Files

A directory may look empty with:

```bash
ls
```

but still contain hidden files.

Check:

```bash
ls -la
```

### Confusing rmdir with rm

`rmdir`:

```text
Only removes empty directories
```

`rm -r`:

```text
Removes directory contents recursively
```

---

## Related Topics

- `mkdir.md`
- `rm.md`
- `../01-Navigation/ls.md`
- `../01-Navigation/cd.md`

---

## Conclusion

Use:

```bash
rmdir DIR
```

when the directory is empty.

It is safer than `rm -r` because it refuses to delete directories that still contain data.