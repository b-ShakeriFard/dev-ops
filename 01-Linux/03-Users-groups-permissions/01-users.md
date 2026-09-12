# 👤 Linux Users

> Understanding local user accounts and how Linux identifies them.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [User Information](#user-information)
- [Important Files](#important-files)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `whoami` | Show current user |
| `id` | Show UID, GID, and groups |
| `id USER` | Show another user's identity |
| `getent passwd USER` | Query user account |
| `who` | Show logged-in users |
| `last` | Show login history |
| `su - USER` | Switch user |

---

## Overview

Linux is a multi-user operating system.

Each user has:

```text
Username
UID
Primary Group
Home Directory
Login Shell
```

Example:

```text
alice:x:1001:1001:Alice:/home/alice:/bin/bash
```

---

## User Information

Check current user:

```bash
whoami
```

Show identity:

```bash
id
```

Example:

```text
uid=1001(alice)
gid=1001(alice)
groups=1001(alice),10(wheel)
```

Important concepts:

```text
UID → User ID
GID → Primary Group ID
```

---

## Important Files

### `/etc/passwd`

Stores account information:

```bash
cat /etc/passwd
```

Format:

```text
username:x:UID:GID:comment:home:shell
```

---

### `/etc/shadow`

Stores password hashes and password-aging information.

```bash
sudo cat /etc/shadow
```

This file is protected and should not be readable by normal users.

---

## Human vs System Users

Human users commonly have:

```text
/home/username
/bin/bash
```

Service accounts may use shells such as:

```text
/usr/sbin/nologin
/bin/false
```

Example:

```text
nginx
mysql
sshd
```

These accounts run services without normal interactive login.

---

## Practical Examples

Check user:

```bash
id alice
```

Find account entry:

```bash
getent passwd alice
```

See logged-in users:

```bash
who
```

Switch user:

```bash
su - alice
```

Check shell:

```bash
getent passwd alice
```

---

## Related Topics

- `groups.md`
- `useradd-usermod.md`
- `passwd.md`
- `sudo.md`

---

## Conclusion

A Linux user is identified internally by a:

```text
UID
```

The username is the human-readable representation of that identity.

Understanding users is the foundation for group membership, ownership, permissions, and privilege management.