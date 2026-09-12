# 🔐 chmod - File Permissions

> Changing read, write, and execute permissions on Linux files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Symbolic Mode](#symbolic-mode)
- [Numeric Mode](#numeric-mode)
- [Directory Permissions](#directory-permissions)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `chmod u+x FILE` | Add execute for owner |
| `chmod g-w FILE` | Remove group write |
| `chmod o+r FILE` | Add read for others |
| `chmod 644 FILE` | Standard file permissions |
| `chmod 755 FILE` | Common executable permissions |
| `chmod -R MODE DIR` | Change recursively |
| `ls -l` | Verify permissions |

---

## Overview

Linux uses three permission types:

```text
r = read
w = write
x = execute
```

Permissions apply to:

```text
u = user
g = group
o = others
a = all
```

Example:

```text
-rwxr-xr--
```

means:

```text
User   → rwx
Group  → r-x
Others → r--
```

---

## Symbolic Mode

Add execute permission:

```bash
chmod u+x script.sh
```

Remove group write:

```bash
chmod g-w file.txt
```

Add read for everyone:

```bash
chmod a+r file.txt
```

Set exact permissions:

```bash
chmod u=rw,g=r,o= file.txt
```

---

## Numeric Mode

Values:

```text
r = 4
w = 2
x = 1
```

Combine them:

```text
7 = rwx
6 = rw-
5 = r-x
4 = r--
```

Examples:

```bash
chmod 644 file.txt
```

Result:

```text
rw-r--r--
```

Executable script:

```bash
chmod 755 script.sh
```

Result:

```text
rwxr-xr-x
```

---

## Directory Permissions

For directories:

```text
r → list contents
w → create/delete entries
x → enter/traverse directory
```

Example:

```bash
chmod 750 /srv/project
```

Result:

```text
Owner  → rwx
Group  → r-x
Others → ---
```

---

## Practical Examples

Make script executable:

```bash
chmod +x deploy.sh
```

Private file:

```bash
chmod 600 secret.txt
```

Shared directory:

```bash
chmod 770 /srv/team
```

Verify:

```bash
ls -ld /srv/team
```

---

## Common Pitfalls

### Using `777`

```bash
chmod 777 file
```

gives everyone full access.

This is rarely appropriate.

### Recursive Changes

Be careful with:

```bash
chmod -R
```

Directories and files often need different execute permissions.

---

## Related Topics

- `ownership.md`
- `umask.md`
- `special-permissions.md`
- `../02-File-System/permissions-basics.md`

---

## Conclusion

`chmod` controls what users can do with files and directories.

The key model is:

```text
Who?
u / g / o

What?
r / w / x
```

For day-to-day administration, both symbolic and numeric modes are worth knowing.