# 👤 File Ownership

> Managing which user and group own files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Changing Ownership](#changing-ownership)
- [Recursive Ownership](#recursive-ownership)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ls -l` | Show owner and group |
| `chown USER FILE` | Change owner |
| `chown USER:GROUP FILE` | Change owner and group |
| `chgrp GROUP FILE` | Change group |
| `chown -R` | Change ownership recursively |
| `stat FILE` | Show detailed metadata |

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
-rw-r----- alice developers report.txt
```

Meaning:

```text
Owner → alice
Group → developers
```

Permissions are then applied to:

```text
User
Group
Others
```

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

Verify:

```bash
ls -l file.txt
```

---

## Recursive Ownership

Change an entire directory tree:

```bash
sudo chown -R alice:developers /srv/project
```

This affects all files and subdirectories.

Use `-R` carefully.

---

## Practical Examples

Give a web application ownership:

```bash
sudo chown -R nginx:nginx /var/www/app
```

Assign shared project group:

```bash
sudo chgrp -R developers /srv/project
```

Check ownership:

```bash
ls -ld /srv/project
```

Detailed metadata:

```bash
stat /srv/project
```

---

## Ownership vs Permissions

Ownership determines **which permission set applies**.

Example:

```text
-rwxr-x--- alice developers deploy.sh
```

If Bob belongs to `developers`, he receives:

```text
r-x
```

group permissions.

---

## Common Pitfalls

### Wrong Owner, Correct Permissions

A service may still fail if the wrong user owns its files.

Check:

```bash
ls -l
```

---

### Dangerous Recursive Change

Avoid commands like:

```bash
sudo chown -R user:group /
```

This can break the operating system.

Always verify the target path first.

---

## Related Topics

- `chmod.md`
- `groups.md`
- `special-permissions.md`
- `../02-File-System/permissions-basics.md`

---

## Conclusion

Linux file access depends on both:

```text
Ownership
+
Permissions
```

Use:

```bash
chown
chgrp
ls -l
```

to manage and verify file ownership.