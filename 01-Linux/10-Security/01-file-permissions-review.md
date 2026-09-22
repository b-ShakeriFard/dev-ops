# 🔑 File Permissions Review

> Understanding Linux ownership, permissions, and access control as the foundation of system security.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Permission Model](#permission-model)
- [Reading Permissions](#reading-permissions)
- [Changing Permissions](#changing-permissions)
- [Changing Ownership](#changing-ownership)
- [Special Permissions](#special-permissions)
- [Practical Examples](#practical-examples)
- [Common Security Mistakes](#common-security-mistakes)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ls -l` | View permissions |
| `chmod` | Change permissions |
| `chown` | Change owner |
| `chgrp` | Change group |
| `umask` | Default permission mask |
| `stat FILE` | Detailed file information |
| `find -perm` | Search by permissions |

---

## Overview

Linux security begins with controlling access to files.

Every file has:

```text
Owner
Group
Permissions
```

Example:

```bash
ls -l config.conf
```

Output:

```text
-rw-r----- 1 alice developers 2048 Sep 23 config.conf
```

---

## Permission Model

Linux permissions are divided into three categories:

```text
Owner
Group
Others
```

And three basic permissions:

```text
r → read
w → write
x → execute
```

Example:

```text
-rwxr-x---
```

Breakdown:

```text
Owner  → rwx
Group  → r-x
Others → ---
```

---

## Reading Permissions

Example:

```text
drwxr-xr-x
```

First character:

```text
d → directory
- → regular file
l → symbolic link
```

Permission groups:

```text
d rwx r-x r-x
  |   |   |
  |   |   └── Others
  |   └────── Group
  └────────── Owner
```

---

## Changing Permissions

Use:

```bash
chmod
```

Symbolic form:

```bash
chmod u+x script.sh
```

Meaning:

```text
u → user/owner
+ → add
x → execute
```

Remove write permission:

```bash
chmod g-w file.txt
```

Numeric form:

```bash
chmod 750 script.sh
```

Meaning:

```text
7 → owner: rwx
5 → group: r-x
0 → others: ---
```

Common secure permissions:

```text
600 → private file
644 → normal file
700 → private executable
755 → public executable
```

---

## Changing Ownership

Change owner:

```bash
chown alice file.txt
```

Change owner and group:

```bash
chown alice:developers file.txt
```

Change group only:

```bash
chgrp developers file.txt
```

Check:

```bash
ls -l file.txt
```

---

## Special Permissions

### SUID

Example:

```text
-rwsr-xr-x
```

The executable runs with the owner's privileges.

Check:

```bash
find / -perm -4000
```

Use carefully.

---

### SGID

On files:

```text
-rwxr-sr-x
```

On directories:

```bash
chmod g+s shared-folder
```

New files inherit the directory group.

---

### Sticky Bit

Common example:

```text
/tmp
```

Permissions:

```text
drwxrwxrwt
```

Users can create files, but only owners can delete their own files.

---

## Practical Examples

Find world-writable files:

```bash
find / -type f -perm -0002
```

Find SUID files:

```bash
find / -perm -4000
```

Secure a private key:

```bash
chmod 600 ~/.ssh/id_rsa
```

Secure a script:

```bash
chmod 750 backup.sh
```

Check detailed information:

```bash
stat backup.sh
```

---

## Common Security Mistakes

### Too Many Permissions

Avoid:

```bash
chmod 777 file
```

This gives everyone:

```text
Read
Write
Execute
```

Prefer the minimum required access.

---

### Incorrect Ownership

A service may fail because files belong to the wrong user.

Check:

```bash
ls -l
```

Fix:

```bash
chown user:group file
```

---

### Ignoring Directory Permissions

For directories:

```text
r → list contents
w → create/delete entries
x → enter directory
```

The meaning differs slightly from files.

---

## Related Topics

- `sudo-security.md`
- `account-security.md`
- `../03-Users-Groups-Permissions/`
- `../05-Processes-Systemd/`

---

## Conclusion

Linux file security is built on:

```text
Ownership
+
Permissions
+
Special access controls
```

The safest approach is:

```text
Grant only what is required.
Remove unnecessary access.
Review permissions regularly.
```