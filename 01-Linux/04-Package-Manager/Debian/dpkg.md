# 📦 dpkg - Debian Package Tool

> Managing `.deb` packages directly on Debian-based systems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Installing Packages](#installing-packages)
- [Querying Packages](#querying-packages)
- [Removing Packages](#removing-packages)
- [Troubleshooting](#troubleshooting)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `dpkg -i FILE.deb` | Install local package |
| `dpkg -r PACKAGE` | Remove package |
| `dpkg -P PACKAGE` | Remove package + config |
| `dpkg -l` | List installed packages |
| `dpkg -L PACKAGE` | List package files |
| `dpkg -S FILE` | Find package owning a file |
| `dpkg -s PACKAGE` | Show package status |

---

## Overview

`dpkg` is the low-level package manager used by Debian-based systems.

It works directly with:

```text
.deb
```

package files.

Unlike APT, `dpkg` does not automatically download missing dependencies.

Think:

```text
APT
 ↓
Dependency + Repository Management
 ↓
dpkg
 ↓
Actual DEB Package Installation
```

---

## Installing Packages

Install a local package:

```bash
sudo dpkg -i app.deb
```

If dependencies are missing:

```bash
sudo apt --fix-broken install
```

APT can then install the required dependencies.

---

## Querying Packages

List installed packages:

```bash
dpkg -l
```

Search:

```bash
dpkg -l | grep nginx
```

Show package status:

```bash
dpkg -s nginx
```

List files installed by package:

```bash
dpkg -L nginx
```

Find which package owns a file:

```bash
dpkg -S /usr/bin/curl
```

---

## Inspect a DEB File

Show package information:

```bash
dpkg-deb -I app.deb
```

List contents:

```bash
dpkg-deb -c app.deb
```

This is useful before installing an unfamiliar package.

---

## Removing Packages

Remove package:

```bash
sudo dpkg -r nginx
```

Remove package and configuration:

```bash
sudo dpkg -P nginx
```

---

## Troubleshooting

### Dependency Errors

Example:

```text
dependency problems prevent configuration
```

Fix:

```bash
sudo apt --fix-broken install
```

---

### Interrupted Installation

Try:

```bash
sudo dpkg --configure -a
```

This attempts to finish configuring unpacked packages.

---

### Check Package State

```bash
dpkg -s PACKAGE
```

or:

```bash
dpkg -l PACKAGE
```

---

## Related Topics

- `apt.md`
- `repositories.md`
- `troubleshooting.md`

---

## Conclusion

`dpkg` works directly with Debian package files.

Use:

```text
APT  → normal package management

dpkg → direct .deb package operations
```

For everyday administration, prefer APT unless you specifically need to work with a local `.deb` file.