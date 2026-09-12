# 📦 APT Package Manager

> Managing software packages on Debian and Ubuntu systems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Installing Packages](#installing-packages)
- [Updating Packages](#updating-packages)
- [Searching Packages](#searching-packages)
- [Removing Packages](#removing-packages)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `apt update` | Refresh repository metadata |
| `apt upgrade` | Upgrade installed packages |
| `apt install PACKAGE` | Install package |
| `apt remove PACKAGE` | Remove package |
| `apt purge PACKAGE` | Remove package + config files |
| `apt search TERM` | Search packages |
| `apt show PACKAGE` | Show package details |
| `apt autoremove` | Remove unused dependencies |

---

## Overview

APT is the high-level package manager used by:

```text
Debian
Ubuntu
Linux Mint
```

It works with:

```text
.deb
```

packages and automatically handles dependencies.

APT uses repositories configured under:

```text
/etc/apt/
```

---

## Installing Packages

Refresh metadata first:

```bash
sudo apt update
```

Install:

```bash
sudo apt install nginx
```

Install multiple packages:

```bash
sudo apt install curl git vim
```

---

## Updating Packages

Refresh available package information:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade
```

A common workflow:

```bash
sudo apt update
sudo apt upgrade
```

---

## Searching Packages

Search:

```bash
apt search nginx
```

Show package details:

```bash
apt show nginx
```

Check installed package:

```bash
apt list --installed nginx
```

---

## Removing Packages

Remove software:

```bash
sudo apt remove nginx
```

Remove software and package configuration:

```bash
sudo apt purge nginx
```

Remove unused dependencies:

```bash
sudo apt autoremove
```

---

## Practical Examples

Install SSH server:

```bash
sudo apt update
sudo apt install openssh-server
```

Check package version:

```bash
apt show openssh-server
```

Upgrade one package:

```bash
sudo apt install --only-upgrade curl
```

---

## Troubleshooting

### Package Not Found

Refresh metadata:

```bash
sudo apt update
```

Then search:

```bash
apt search PACKAGE
```

---

### Broken Dependencies

Try:

```bash
sudo apt --fix-broken install
```

---

### Package Manager Locked

Another package process may be running.

Check:

```bash
ps aux | grep -E 'apt|dpkg'
```

Avoid deleting lock files unless you understand why the lock exists.

---

## Related Topics

- `dpkg.md`
- `repositories.md`
- `troubleshooting.md`

---

## Conclusion

APT is the main day-to-day package management tool on Debian-based systems.

The core workflow is:

```text
apt update
   ↓
apt install / upgrade
   ↓
apt remove / purge
```

Use APT for normal administration and `dpkg` when working directly with `.deb` files.