# 👥 Linux Groups

> Organizing users and managing shared access in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Primary vs Supplementary Groups](#primary-vs-supplementary-groups)
- [Important Files](#important-files)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `groups` | Show current user's groups |
| `groups USER` | Show another user's groups |
| `id USER` | Show UID, GID, and groups |
| `groupadd GROUP` | Create group |
| `groupdel GROUP` | Delete group |
| `getent group GROUP` | Query group information |
| `usermod -aG GROUP USER` | Add user to supplementary group |

---

## Overview

Groups allow multiple users to share access to files and resources.

Example:

```text
developers
├── alice
├── bob
└── carol
```

A file owned by group `developers` can grant permissions to all members of that group.

---

## Primary vs Supplementary Groups

Each user has one **primary group**.

Check:

```bash
id alice
```

Example:

```text
uid=1001(alice)
gid=1001(alice)
groups=1001(alice),10(wheel),2000(developers)
```

Here:

```text
Primary group → alice
Supplementary groups → wheel, developers
```

---

## Important Files

### `/etc/group`

Stores group definitions.

```bash
cat /etc/group
```

Format:

```text
group_name:x:GID:members
```

Example:

```text
developers:x:2000:alice,bob
```

---

## Practical Examples

Create group:

```bash
sudo groupadd developers
```

Add user:

```bash
sudo usermod -aG developers alice
```

Check membership:

```bash
id alice
```

or:

```bash
groups alice
```

Remove group:

```bash
sudo groupdel developers
```

---

## Shared Access Example

Directory:

```text
/projects
```

Assign group:

```bash
sudo chgrp developers /projects
```

Allow group access:

```bash
sudo chmod 770 /projects
```

Now members of `developers` can access the directory.

---

## Common Pitfall

This is dangerous:

```bash
usermod -G developers alice
```

because it replaces supplementary groups.

Safer:

```bash
usermod -aG developers alice
```

`-a` means:

```text
append
```

---

## Related Topics

- `users.md`
- `useradd-usermod.md`
- `ownership.md`
- `chmod.md`

---

## Conclusion

Groups simplify Linux access control by allowing permissions to be assigned to multiple users at once.

The key relationship is:

```text
User
 ↓
Group Membership
 ↓
File Group Ownership
 ↓
Group Permissions
```