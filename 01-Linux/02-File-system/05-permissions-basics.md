# 🔐 Permission Basics

> Understanding how Linux controls access to files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Permission Groups](#permission-groups)
- [Permission Types](#permission-types)
- [Reading Permissions](#reading-permissions)
- [Practical Examples](#practical-examples)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ls -l` | Show permissions |
| `chmod MODE FILE` | Change permissions |
| `chown USER FILE` | Change owner |
| `chgrp GROUP FILE` | Change group |
| `stat FILE` | Show detailed metadata |

---

## Overview

Linux permissions control:

```text
Who can access a file
and
What they can do with it
```

Permissions are divided into three groups:

```text
User
Group
Others
```

---

## Permission Groups

Example:

```text
-rwxr-xr--
```

Split it like this:

```text
-   rwx   r-x   r--
    User  Group Others
```

The first character describes the file type.

The remaining nine characters describe permissions.

---

## Permission Types

Linux uses three main permissions:

| Permission | Symbol | Meaning |
|---|---|---|
| Read | `r` | View content |
| Write | `w` | Modify content |
| Execute | `x` | Run file / enter directory |

---

## Reading Permissions

Example:

```text
-rwxr-xr--
```

Meaning:

```text
User:
rwx
Read + Write + Execute

Group:
r-x
Read + Execute

Others:
r--
Read only
```

---

## File vs Directory Permissions

Permissions behave slightly differently on directories.

### Directory Read

```text
r
```

Allows listing directory contents.

### Directory Write

```text
w
```

Allows creating or deleting entries.

### Directory Execute

```text
x
```

Allows entering/traversing the directory.

Example:

```bash
cd /data
```

requires execute permission.

---

## Practical Examples

Check permissions:

```bash
ls -l file.txt
```

Example:

```text
-rw-r--r--  alice developers file.txt
```

Meaning:

```text
Owner: alice
Group: developers
```

Change permissions:

```bash
chmod u+x script.sh
```

Remove write permission from group:

```bash
chmod g-w file.txt
```

Add read permission for others:

```bash
chmod o+r file.txt
```

---

## Numeric Permissions

Linux also supports octal notation:

```text
r = 4
w = 2
x = 1
```

Example:

```text
7 = rwx
6 = rw-
5 = r-x
4 = r--
```

Set:

```bash
chmod 755 script.sh
```

Result:

```text
rwxr-xr-x
```

---

## Related Topics

- `ownership.md`
- `../03-Users-Groups-Permissions/chmod.md`
- `../03-Users-Groups-Permissions/ownership.md`

---

## Conclusion

Linux permissions answer two questions:

```text
Who?

User / Group / Others

What?

Read / Write / Execute
```

This page covers the basics; detailed permission management belongs in the next chapter.