# ⭐ Special Permissions

> Understanding SUID, SGID, and the Sticky Bit in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [SUID](#suid)
- [SGID](#sgid)
- [Sticky Bit](#sticky-bit)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Permission | Numeric | Purpose |
|---|---:|---|
| SUID | `4xxx` | Run executable as file owner |
| SGID | `2xxx` | Run as group / inherit directory group |
| Sticky Bit | `1xxx` | Restrict deletion in shared directories |

Useful commands:

```bash
chmod u+s FILE
chmod g+s FILE
chmod +t DIR
```

---

## Overview

Normal Linux permissions use:

```text
r
w
x
```

Special permissions add extra behavior:

```text
SUID
SGID
Sticky Bit
```

They appear in the execute positions shown by:

```bash
ls -l
```

---

## SUID

SUID means:

```text
Set User ID
```

When an executable has SUID, it runs with the effective privileges of the **file owner**.

Example:

```bash
ls -l /usr/bin/passwd
```

You may see:

```text
-rwsr-xr-x
```

The `s` in the owner's execute position indicates SUID.

Set:

```bash
chmod u+s program
```

Numeric example:

```bash
chmod 4755 program
```

---

## SGID

SGID means:

```text
Set Group ID
```

On executables, the process runs using the file's group.

On directories, newly created files inherit the directory's group.

Example:

```bash
chmod g+s /srv/project
```

Numeric:

```bash
chmod 2770 /srv/project
```

This is useful for shared team directories.

---

## Sticky Bit

The Sticky Bit is commonly used on shared writable directories.

Example:

```text
/tmp
```

Check:

```bash
ls -ld /tmp
```

Typical:

```text
drwxrwxrwt
```

The final:

```text
t
```

indicates the Sticky Bit.

Users can create files, but normally cannot delete files owned by other users.

Set:

```bash
chmod +t /shared
```

Numeric:

```bash
chmod 1777 /shared
```

---

## Practical Examples

Find SUID files:

```bash
find / -perm -4000 2>/dev/null
```

Find SGID files:

```bash
find / -perm -2000 2>/dev/null
```

Shared team directory:

```bash
chgrp developers /srv/project
chmod 2770 /srv/project
```

---

## Security Note

Unexpected SUID or SGID executables can create privilege-escalation risks.

Audit them regularly:

```bash
find / -perm /6000 -type f 2>/dev/null
```

---

## Related Topics

- `chmod.md`
- `ownership.md`
- `umask.md`
- `troubleshooting.md`

---

## Conclusion

The three special permissions are:

```text
SUID   → execute as owner
SGID   → execute/inherit group
Sticky → protect files in shared directories
```

They are powerful tools, but should be used carefully.