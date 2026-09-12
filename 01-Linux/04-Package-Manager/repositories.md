# 🗃️ Linux Package Repositories

> Understanding where Linux package managers find software and updates.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [APT Repositories](#apt-repositories)
- [DNF Repositories](#dnf-repositories)
- [Practical Examples](#practical-examples)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `apt update` | Refresh APT metadata |
| `apt-cache policy PACKAGE` | Show package source/version |
| `dnf repolist` | List enabled repositories |
| `dnf repolist --all` | Show all repositories |
| `dnf makecache` | Refresh DNF metadata |
| `dnf info PACKAGE` | Show package information |
| `dnf --enablerepo=REPO ...` | Temporarily enable repo |
| `dnf --disablerepo=REPO ...` | Temporarily disable repo |

---

## Overview

A repository is a software source containing:

```text
Packages
Metadata
Versions
Dependencies
Signatures
```

The package manager queries repositories instead of requiring users to download packages manually.

```text
Linux Host
   ↓
Package Manager
   ↓
Repository
   ↓
Package + Dependencies
```

---

## APT Repositories

APT configuration is stored under:

```text
/etc/apt/
```

Common locations include:

```text
/etc/apt/sources.list
/etc/apt/sources.list.d/
```

Refresh repository metadata:

```bash
sudo apt update
```

Check where a package comes from:

```bash
apt-cache policy nginx
```

---

## DNF Repositories

Repository files are commonly stored in:

```text
/etc/yum.repos.d/
```

Example:

```text
/etc/yum.repos.d/appstream.repo
```

List enabled repositories:

```bash
dnf repolist
```

Show all:

```bash
dnf repolist --all
```

Refresh metadata:

```bash
sudo dnf makecache
```

---

## Practical Examples

Install from a specific DNF repository:

```bash
sudo dnf \
--enablerepo=myrepo \
install nginx
```

Temporarily disable a repository:

```bash
sudo dnf \
--disablerepo=myrepo \
upgrade
```

Check available package versions:

```bash
dnf list --showduplicates nginx
```

APT equivalent:

```bash
apt-cache policy nginx
```

---

## GPG Verification

Repositories commonly use cryptographic signatures to verify package authenticity.

Avoid disabling signature verification unless there is a very specific, trusted reason.

Repository trust is part of the software supply chain.

---

## Troubleshooting

### Repository Unreachable

APT:

```bash
sudo apt update
```

DNF:

```bash
sudo dnf makecache
```

Check:

- DNS
- Network connectivity
- Proxy settings
- Repository URL

---

### Package Not Found

APT:

```bash
apt search PACKAGE
apt-cache policy PACKAGE
```

DNF:

```bash
dnf search PACKAGE
dnf repolist
```

---

### Stale Metadata

APT:

```bash
sudo apt update
```

DNF:

```bash
sudo dnf clean all
sudo dnf makecache
```

---

## Related Topics

- `apt.md`
- `dnf.md`
- `rpm.md`
- `troubleshooting.md`

---

## Conclusion

Repositories are the source of packages, dependencies, and updates.

The core relationship is:

```text
Repository
   ↓
Package Manager
   ↓
Installed Software
```

When package installation fails, always check repository availability before blaming the package itself.