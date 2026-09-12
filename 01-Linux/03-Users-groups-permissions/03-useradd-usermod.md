# 🛠️ useradd & usermod

> Creating and modifying Linux user accounts.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Creating Users](#creating-users)
- [Modifying Users](#modifying-users)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `useradd USER` | Create user |
| `useradd -m USER` | Create user with home directory |
| `useradd -s SHELL USER` | Set login shell |
| `useradd -u UID USER` | Set UID |
| `usermod -aG GROUP USER` | Add supplementary group |
| `usermod -s SHELL USER` | Change shell |
| `usermod -d DIR -m USER` | Move home directory |
| `userdel USER` | Delete user |

---

## Creating Users

Basic:

```bash
sudo useradd alice
```

Create with home directory:

```bash
sudo useradd -m alice
```

Set shell:

```bash
sudo useradd \
-m \
-s /bin/bash \
alice
```

Set specific UID:

```bash
sudo useradd \
-u 1500 \
-m \
alice
```

Verify:

```bash
id alice
```

---

## Creating a Non-Interactive User

Useful for service accounts:

```bash
sudo useradd \
-M \
-s /usr/sbin/nologin \
appuser
```

Meaning:

```text
-M → no home directory
nologin → interactive login disabled
```

---

## Modifying Users

Add to group:

```bash
sudo usermod -aG developers alice
```

Change shell:

```bash
sudo usermod \
-s /bin/bash \
alice
```

Change home directory:

```bash
sudo usermod \
-d /data/alice \
-m \
alice
```

Change username:

```bash
sudo usermod \
-l alice2 \
alice
```

---

## Practical Examples

Create DevOps user:

```bash
sudo useradd \
-m \
-s /bin/bash \
devops
```

Set password:

```bash
sudo passwd devops
```

Add sudo group:

RHEL/Rocky:

```bash
sudo usermod -aG wheel devops
```

Ubuntu:

```bash
sudo usermod -aG sudo devops
```

---

## Common Pitfalls

### Forgetting `-a`

This:

```bash
usermod -G developers alice
```

replaces supplementary groups.

Use:

```bash
usermod -aG developers alice
```

---

### Home Directory Not Created

Depending on distribution/defaults:

```bash
useradd alice
```

may not create `/home/alice`.

Explicitly use:

```bash
useradd -m alice
```

---

## Related Topics

- `users.md`
- `groups.md`
- `passwd.md`
- `sudo.md`

---

## Conclusion

The core tools are:

```text
useradd → create account
usermod → modify account
userdel → remove account
```

Always verify changes with:

```bash
id USER
getent passwd USER
```