# 🛠️ Users, Groups & Permissions Troubleshooting

> Diagnosing common Linux identity, ownership, and permission problems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Permission Denied](#permission-denied)
- [Group Membership Issues](#group-membership-issues)
- [sudo Problems](#sudo-problems)
- [Login Problems](#login-problems)
- [Ownership Problems](#ownership-problems)
- [Troubleshooting Workflow](#troubleshooting-workflow)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `id USER` | Check UID and groups |
| `groups USER` | Show group membership |
| `ls -l FILE` | Check permissions and ownership |
| `namei -l PATH` | Check permissions along a path |
| `getent passwd USER` | Verify account |
| `sudo -l` | Check sudo privileges |
| `passwd -S USER` | Check password status |
| `chage -l USER` | Check account aging |

---

## Permission Denied

Example:

```text
Permission denied
```

Check:

```bash
ls -l file.txt
```

Then check ownership:

```bash
stat file.txt
```

For directories:

```bash
namei -l /srv/app/config/file.txt
```

A missing execute permission on any parent directory can block access.

---

## Group Membership Issues

Check:

```bash
id alice
```

Add user:

```bash
sudo usermod -aG developers alice
```

New group membership may require a new login session.

Verify:

```bash
groups alice
```

---

## sudo Problems

Check privileges:

```bash
sudo -l
```

Check membership:

```bash
id
```

RHEL/Rocky commonly use:

```text
wheel
```

Ubuntu/Debian commonly use:

```text
sudo
```

Edit rules safely:

```bash
sudo visudo
```

---

## Login Problems

Check account:

```bash
getent passwd alice
```

Check password status:

```bash
sudo passwd -S alice
```

Check expiration:

```bash
sudo chage -l alice
```

Check login shell:

```bash
getent passwd alice
```

Accounts using:

```text
/usr/sbin/nologin
```

cannot normally start interactive sessions.

---

## Ownership Problems

A service may have correct permissions but the wrong owner.

Check:

```bash
ls -l /srv/app
```

Fix:

```bash
sudo chown -R appuser:appgroup /srv/app
```

Use recursive changes carefully.

---

## Common Scenario

A script will not execute:

```bash
./deploy.sh
```

Check:

```bash
ls -l deploy.sh
```

Add execute permission:

```bash
chmod +x deploy.sh
```

If it still fails, check the directory path:

```bash
namei -l "$(pwd)/deploy.sh"
```

---

## Troubleshooting Workflow

```text
Who am I?
   ↓
id

Who owns the file?
   ↓
ls -l

What permissions apply?
   ↓
r / w / x

Can I traverse the path?
   ↓
namei -l

Is group membership correct?
   ↓
groups

Is privilege escalation required?
   ↓
sudo -l
```

---

## Related Topics

- `users.md`
- `groups.md`
- `sudo.md`
- `ownership.md`
- `chmod.md`

---

## Conclusion

Most access problems come down to:

```text
Wrong user
Wrong group
Wrong owner
Wrong permissions
or
Missing sudo privileges
```

Start with:

```bash
id
ls -l
namei -l
sudo -l
```

and work from identity to access.