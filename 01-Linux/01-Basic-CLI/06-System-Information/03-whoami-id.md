# 👤 whoami & id

> Identifying the current user, UID, GID, and group memberships.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using whoami](#using-whoami)
- [Using id](#using-id)
- [Checking Another User](#checking-another-user)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `whoami` | Show current username |
| `id` | Show UID, GID, and groups |
| `id USER` | Show identity information for a user |
| `id -u` | Show current UID |
| `id -g` | Show current primary GID |
| `id -Gn` | Show group names |
| `groups` | Show group memberships |

---

## Overview

`whoami` answers:

```text
Which user am I currently operating as?
```

`id` provides more detail:

```text
Username
UID
Primary group
GID
Supplementary groups
```

These commands are useful when troubleshooting permissions and `sudo` access.

---

## Using whoami

Run:

```bash
whoami
```

Example:

```text
alice
```

After switching users:

```bash
sudo -i
whoami
```

Output may be:

```text
root
```

---

## Using id

Run:

```bash
id
```

Example:

```text
uid=1001(alice) gid=1001(alice) groups=1001(alice),10(wheel)
```

This tells you:

```text
UID    → user ID
GID    → primary group ID
groups → supplementary groups
```

Show only UID:

```bash
id -u
```

Show primary GID:

```bash
id -g
```

Show group names:

```bash
id -Gn
```

---

## Checking Another User

Inspect another account:

```bash
id bob
```

Example:

```text
uid=1002(bob) gid=1002(bob) groups=1002(bob),10(wheel)
```

This is useful for checking whether a user belongs to groups required for access.

---

## Practical Examples

Check whether you are root:

```bash
id -u
```

Root normally has:

```text
UID 0
```

Check current groups:

```bash
groups
```

Verify sudo-related group membership:

```bash
id
```

On RHEL-style systems, look for:

```text
wheel
```

On Ubuntu/Debian, commonly:

```text
sudo
```

---

## Common Pitfalls

### whoami vs who

These are different commands.

```bash
whoami
```

shows the current effective username.

```bash
who
```

shows users currently logged into the system.

### Username Is Not the UID

Linux internally identifies users primarily by numeric IDs.

Example:

```text
alice → UID 1001
root  → UID 0
```

### Group Changes May Not Appear Immediately

After adding a user to a group, a new login session may be required before the membership is reflected in the user's session.

---

## Related Topics

- `hostname-uname.md`
- `uptime.md`
- `../../../03-Users-Groups-Permissions/users.md`
- `../../../03-Users-Groups-Permissions/groups.md`

---

## Conclusion

Use:

```bash
whoami
id
```

to quickly answer:

```text
Who am I?
What permissions context am I operating under?
```

For permission problems, `id` is often one of the first commands to run.