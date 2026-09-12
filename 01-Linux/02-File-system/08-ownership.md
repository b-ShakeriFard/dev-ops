# 👤 File Ownership in Linux

> Understanding how users and groups own files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [User and Group Ownership](#user-and-group-ownership)
- [Changing Ownership](#changing-ownership)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ls -l` | Show owner and group |
| `chown USER FILE` | Change owner |
| `chown USER:GROUP FILE` | Change owner and group |
| `chgrp GROUP FILE` | Change group |
| `id USER` | Show user and group membership |
| `stat FILE` | Show detailed ownership metadata |

---

## Overview

Every Linux file and directory has:

```text
Owner
+
Group
```

Example:

```text
-rw-r--r--  alice developers report.txt
```

Meaning:

```text
Owner → alice
Group → developers
```

Permissions are then applied separately to:

```text
User
Group
Others
```

---

## User and Group Ownership

Check:

```bash
ls -l file.txt
```

Example:

```text
-rw-r-----  alice developers file.txt
```

This means:

- `alice` owns the file
- Members of `developers` use the group permissions
- Everyone else uses the `others` permissions

---

## Changing Ownership

Change owner:

```bash
sudo chown bob file.txt
```

Change owner and group:

```bash
sudo chown bob:admins file.txt
```

Change only the group:

```bash
sudo chgrp admins file.txt
```

---

## Recursive Ownership

Change an entire directory tree:

```bash
sudo chown -R bob:admins /data/project
```

⚠️ Be careful with `-R`, especially on system directories.

---

## Practical Examples

Check ownership:

```bash
ls -ld /var/www
```

Change web content owner:

```bash
sudo chown -R nginx:nginx /var/www/app
```

Check user groups:

```bash
id nginx
```

Detailed metadata:

```bash
stat /var/www/app
```

---

## Common Pitfalls

### Correct Permissions, Wrong Owner

A file may show:

```text
-rw-------
```

but if the wrong user owns it, the intended process may still fail.

Check:

```bash
ls -l
```

---

### Recursive chown on the Wrong Path

Commands such as:

```bash
chown -R user:group /
```

can severely damage a Linux system.

Always verify the target path first.

---

## Related Topics

- `permissions-basics.md`
- `../03-Users-Groups-Permissions/users.md`
- `../03-Users-Groups-Permissions/chmod.md`

---

## Conclusion

Linux access control depends on both:

```text
Ownership
+
Permissions
```

The owner and group determine **which permission set applies** to a user accessing a file.