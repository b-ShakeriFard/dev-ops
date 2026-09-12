# 📦 DNF Package Manager

> Managing software packages on RHEL, Rocky Linux, AlmaLinux, and Fedora.

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
| `dnf install PACKAGE` | Install package |
| `dnf remove PACKAGE` | Remove package |
| `dnf upgrade` | Upgrade installed packages |
| `dnf check-update` | Check available updates |
| `dnf search TERM` | Search packages |
| `dnf info PACKAGE` | Show package details |
| `dnf list installed` | List installed packages |
| `dnf clean all` | Clear cached metadata |

---

## Overview

DNF is the high-level package manager used by modern RPM-based systems.

Common distributions:

```text
RHEL
Rocky Linux
AlmaLinux
Fedora
```

DNF works with:

```text
.rpm
```

packages and automatically manages:

- Dependencies
- Repositories
- Updates
- Package metadata

---

## Installing Packages

Install:

```bash
sudo dnf install nginx
```

Install multiple packages:

```bash
sudo dnf install git curl vim
```

Install a local RPM:

```bash
sudo dnf install ./package.rpm
```

Using DNF for local RPMs is useful because it resolves dependencies automatically.

---

## Updating Packages

Check available updates:

```bash
dnf check-update
```

Upgrade system:

```bash
sudo dnf upgrade
```

Upgrade one package:

```bash
sudo dnf upgrade nginx
```

---

## Searching Packages

Search:

```bash
dnf search nginx
```

Show information:

```bash
dnf info nginx
```

Check installed packages:

```bash
dnf list installed
```

Search installed packages:

```bash
dnf list installed | grep nginx
```

---

## Removing Packages

Remove:

```bash
sudo dnf remove nginx
```

Remove unused dependencies:

```bash
sudo dnf autoremove
```

---

## Repository Commands

List repositories:

```bash
dnf repolist
```

Show all:

```bash
dnf repolist --all
```

Install from a specific repository:

```bash
sudo dnf \
--enablerepo=REPO \
install PACKAGE
```

---

## Troubleshooting

### Package Not Found

Search:

```bash
dnf search PACKAGE
```

Check repositories:

```bash
dnf repolist
```

---

### Metadata Problems

Clean cache:

```bash
sudo dnf clean all
```

Rebuild metadata:

```bash
sudo dnf makecache
```

---

### Dependency Conflicts

Check:

```bash
sudo dnf check
```

Avoid forcing package changes before understanding the dependency conflict.

---

## Related Topics

- `rpm.md`
- `repositories.md`
- `troubleshooting.md`

---

## Conclusion

DNF is the main package-management tool for modern Enterprise Linux systems.

The basic workflow is:

```text
Search
  ↓
Install
  ↓
Update
  ↓
Remove
```

For normal administration, prefer DNF over using `rpm` directly.