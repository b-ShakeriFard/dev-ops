# 🛡️ sudo - Administrative Privileges

> Running commands with elevated privileges without logging in directly as root.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using sudo](#using-sudo)
- [sudoers Configuration](#sudoers-configuration)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `sudo COMMAND` | Run command as root |
| `sudo -u USER COMMAND` | Run as another user |
| `sudo -i` | Open root login shell |
| `sudo -l` | Show allowed sudo commands |
| `visudo` | Safely edit sudoers |
| `id` | Check group membership |

---

## Overview

`sudo` allows authorized users to run commands with elevated privileges.

Instead of logging in directly as:

```text
root
```

a user can run:

```bash
sudo systemctl restart nginx
```

This improves accountability and reduces unnecessary root sessions.

---

## Using sudo

Run a command as root:

```bash
sudo dnf update
```

Run as another user:

```bash
sudo -u postgres psql
```

Open a root shell:

```bash
sudo -i
```

Check allowed commands:

```bash
sudo -l
```

---

## sudoers Configuration

Main configuration:

```text
/etc/sudoers
```

Additional rules are commonly stored in:

```text
/etc/sudoers.d/
```

Always edit using:

```bash
sudo visudo
```

Example rule:

```text
alice ALL=(ALL) ALL
```

Allow one command:

```text
alice ALL=(root) /usr/bin/systemctl restart nginx
```

---

## Group-Based Access

On RHEL/Rocky:

```text
wheel
```

On Ubuntu/Debian:

```text
sudo
```

Example:

```bash
sudo usermod -aG wheel alice
```

Verify:

```bash
id alice
```

---

## Practical Examples

Restart service:

```bash
sudo systemctl restart sshd
```

Edit protected file:

```bash
sudo vi /etc/hosts
```

Check privileges:

```bash
sudo -l
```

Run command as application user:

```bash
sudo -u nginx id
```

---

## Common Pitfalls

### Editing `/etc/sudoers` Directly

Avoid:

```bash
vi /etc/sudoers
```

Use:

```bash
visudo
```

It validates syntax before saving.

### Overly Broad NOPASSWD Rules

Example:

```text
alice ALL=(ALL) NOPASSWD: ALL
```

This grants very broad privilege and should be used cautiously.

---

## Related Topics

- `users.md`
- `groups.md`
- `useradd-usermod.md`
- `troubleshooting.md`

---

## Conclusion

`sudo` provides controlled administrative access without requiring direct root login.

The key commands are:

```bash
sudo COMMAND
sudo -l
visudo
```

For production systems, grant only the privileges users actually need.