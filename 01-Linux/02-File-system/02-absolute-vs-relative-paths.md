# 🧭 Absolute vs Relative Paths

> Understanding how Linux identifies the location of files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Absolute Paths](#absolute-paths)
- [Relative Paths](#relative-paths)
- [Special Path Symbols](#special-path-symbols)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Symbol / Command | Meaning |
|---|---|
| `/` | Filesystem root |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Current user's home |
| `pwd` | Show current directory |
| `realpath FILE` | Show absolute path |

---

## Overview

Linux supports two main ways to reference files:

```text
Absolute Path
Relative Path
```

The difference is where the path starts.

---

## Absolute Paths

An absolute path starts from:

```text
/
```

Example:

```text
/home/alice/project/app.py
```

This path works regardless of your current directory.

Example:

```bash
cat /etc/hosts
```

Because `/etc/hosts` starts from `/`, Linux knows exactly where to look.

---

## Relative Paths

A relative path starts from your current directory.

Suppose:

```bash
pwd
```

returns:

```text
/home/alice
```

Then:

```bash
cd project
```

means:

```text
/home/alice/project
```

Another example:

```bash
cat project/app.py
```

---

## Special Path Symbols

### Current Directory

```text
.
```

Example:

```bash
./script.sh
```

### Parent Directory

```text
..
```

Example:

```bash
cd ..
```

### Home Directory

```text
~
```

Example:

```bash
cd ~
```

Equivalent to something like:

```text
/home/alice
```

---

## Practical Examples

Show current location:

```bash
pwd
```

Move using absolute path:

```bash
cd /var/log
```

Move using relative path:

```bash
cd ../tmp
```

Show full path:

```bash
realpath file.txt
```

Example:

```text
/home/alice/project/file.txt
```

---

## Common Mistake

These are different:

```text
etc/hosts
```

and:

```text
/etc/hosts
```

The first is relative.

The second is absolute.

---

## Related Topics

- `filesystem-hierarchy.md`
- `hidden-files.md`
- `searching-files.md`

---

## Conclusion

Use absolute paths when you need an exact location.

Use relative paths when working within a known directory structure.

The key distinction:

```text
Starts with /  → Absolute

Does not       → Relative
```