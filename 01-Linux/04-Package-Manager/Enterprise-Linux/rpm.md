# 📦 rpm - RPM Package Manager

> Working directly with `.rpm` packages on Enterprise Linux systems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Installing Packages](#installing-packages)
- [Querying Packages](#querying-packages)
- [Verifying Packages](#verifying-packages)
- [Removing Packages](#removing-packages)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `rpm -ivh FILE.rpm` | Install package |
| `rpm -Uvh FILE.rpm` | Upgrade/install package |
| `rpm -e PACKAGE` | Remove package |
| `rpm -qa` | List installed packages |
| `rpm -qi PACKAGE` | Show package information |
| `rpm -ql PACKAGE` | List installed files |
| `rpm -qf FILE` | Find package owning a file |
| `rpm -V PACKAGE` | Verify installed package |

---

## Overview

`rpm` is the low-level package tool used by RPM-based distributions.

Common systems:

```text
RHEL
Rocky Linux
AlmaLinux
Fedora
```

It works directly with:

```text
.rpm
```

files.

Unlike DNF, `rpm` does not automatically resolve missing dependencies.

---

## Installing Packages

Install:

```bash
sudo rpm -ivh package.rpm
```

Options:

```text
-i → install
-v → verbose
-h → progress display
```

Upgrade or install:

```bash
sudo rpm -Uvh package.rpm
```

For most local RPM installs, prefer:

```bash
sudo dnf install ./package.rpm
```

because DNF handles dependencies.

---

## Querying Packages

Check whether a package is installed:

```bash
rpm -q nginx
```

List all installed packages:

```bash
rpm -qa
```

Show information:

```bash
rpm -qi nginx
```

List files:

```bash
rpm -ql nginx
```

Find which package owns a file:

```bash
rpm -qf /usr/bin/curl
```

---

## Inspect an RPM File

Show package information before installation:

```bash
rpm -qip package.rpm
```

List its files:

```bash
rpm -qlp package.rpm
```

---

## Verifying Packages

Verify installed files against package metadata:

```bash
rpm -V nginx
```

No output usually means no differences were detected.

Verify all packages:

```bash
rpm -Va
```

This can produce a large amount of output.

---

## Removing Packages

Remove:

```bash
sudo rpm -e nginx
```

If dependencies require the package, RPM reports the conflict.

Prefer DNF when dependency handling is needed:

```bash
sudo dnf remove nginx
```

---

## Troubleshooting

### Failed Dependency

Example:

```text
Failed dependencies:
```

Use:

```bash
sudo dnf install ./package.rpm
```

instead of forcing the RPM installation.

---

### Identify an Unknown File

```bash
rpm -qf /path/to/file
```

Useful when investigating system binaries or configuration files.

---

### Check Package Integrity

```bash
rpm -V PACKAGE
```

This helps detect modified or missing package-managed files.

---

## Related Topics

- `dnf.md`
- `repositories.md`
- `troubleshooting.md`

---

## Conclusion

`rpm` works directly with RPM packages and the installed package database.

Use:

```text
DNF → normal administration and dependencies

RPM → direct package inspection and low-level operations
```

For everyday package management, DNF should usually be your first choice.