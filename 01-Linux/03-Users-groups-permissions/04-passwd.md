# 🔑 passwd - Password Management

> Setting passwords and managing basic password aging in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Set or Change Password](#set-or-change-password)
- [Lock and Unlock Accounts](#lock-and-unlock-accounts)
- [Password Aging](#password-aging)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `passwd` | Change your own password |
| `passwd USER` | Set another user's password |
| `passwd -l USER` | Lock password authentication |
| `passwd -u USER` | Unlock password authentication |
| `passwd -S USER` | Show password status |
| `chage -l USER` | Show password aging |
| `chage -M DAYS USER` | Set maximum password age |

---

## Overview

Linux stores account information in:

```text
/etc/passwd
```

Password hashes are stored separately in:

```text
/etc/shadow
```

Normal users cannot read `/etc/shadow`.

---

## Set or Change Password

Change your own password:

```bash
passwd
```

Set another user's password:

```bash
sudo passwd alice
```

Linux prompts for the new password interactively.

---

## Lock and Unlock Accounts

Lock password authentication:

```bash
sudo passwd -l alice
```

Unlock:

```bash
sudo passwd -u alice
```

Check status:

```bash
passwd -S alice
```

> Locking a password does not necessarily disable every possible login method, such as SSH key authentication.

---

## Password Aging

View aging information:

```bash
sudo chage -l alice
```

Set maximum password age to 90 days:

```bash
sudo chage -M 90 alice
```

Set minimum age:

```bash
sudo chage -m 1 alice
```

Force password change at next login:

```bash
sudo chage -d 0 alice
```

---

## Practical Examples

Create account:

```bash
sudo useradd -m alice
```

Set password:

```bash
sudo passwd alice
```

Check status:

```bash
sudo passwd -S alice
```

Force password change on first login:

```bash
sudo chage -d 0 alice
```

---

## Common Pitfall

Account locking and account expiration are different concepts.

Password lock:

```bash
passwd -l alice
```

Account expiration can be managed with:

```bash
chage
```

or user-management options.

---

## Related Topics

- `users.md`
- `useradd-usermod.md`
- `sudo.md`
- `troubleshooting.md`

---

## Conclusion

Use:

```text
passwd → manage passwords
chage  → manage password aging
```

For administration, the most useful checks are:

```bash
passwd -S USER
chage -l USER
```