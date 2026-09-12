# 🛠️ Package Management Troubleshooting

> Diagnosing repository, dependency, lock, and package installation problems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Package Not Found](#package-not-found)
- [Dependency Problems](#dependency-problems)
- [Repository Problems](#repository-problems)
- [Package Manager Locks](#package-manager-locks)
- [Corrupted Metadata](#corrupted-metadata)
- [Troubleshooting Workflow](#troubleshooting-workflow)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `apt update` | Refresh APT metadata |
| `apt --fix-broken install` | Fix broken Debian dependencies |
| `dpkg --configure -a` | Finish interrupted dpkg setup |
| `dnf check` | Check RPM package problems |
| `dnf clean all` | Clear DNF cache |
| `dnf makecache` | Rebuild DNF metadata |
| `dnf repolist` | Check enabled repositories |
| `rpm -V PACKAGE` | Verify installed RPM files |

---

## Package Not Found

APT:

```bash
sudo apt update
apt search PACKAGE
```

DNF:

```bash
dnf search PACKAGE
dnf repolist
```

Possible causes:

- Repository disabled
- Metadata stale
- Wrong package name
- Package unavailable for the distribution/version

---

## Dependency Problems

Debian/Ubuntu:

```bash
sudo apt --fix-broken install
```

If configuration was interrupted:

```bash
sudo dpkg --configure -a
```

RHEL/Rocky:

```bash
sudo dnf check
```

Prefer resolving dependencies with DNF rather than forcing RPM operations.

---

## Repository Problems

APT:

```bash
sudo apt update
```

DNF:

```bash
sudo dnf makecache
```

Check:

```text
DNS
Network connectivity
Proxy configuration
Repository URL
GPG configuration
```

For DNF:

```bash
dnf repolist --all
```

---

## Package Manager Locks

APT or dpkg may report that another process holds a lock.

Check:

```bash
ps aux | grep -E 'apt|dpkg'
```

Another update process may already be running.

Do not immediately delete lock files.

---

## Corrupted Metadata

APT:

```bash
sudo apt update
```

DNF:

```bash
sudo dnf clean all
sudo dnf makecache
```

Then retry the operation.

---

## Local Package Fails

DEB:

```bash
sudo dpkg -i package.deb
sudo apt --fix-broken install
```

RPM:

Prefer:

```bash
sudo dnf install ./package.rpm
```

instead of:

```bash
rpm -ivh package.rpm
```

when dependencies are required.

---

## Package Installed but Command Missing

Check package contents.

Debian:

```bash
dpkg -L PACKAGE
```

RPM:

```bash
rpm -ql PACKAGE
```

Check executable path:

```bash
command -v COMMAND
```

---

## Troubleshooting Workflow

```text
Package Operation Fails
        ↓
Check package name
        ↓
Refresh metadata
        ↓
Check repositories
        ↓
Check dependencies
        ↓
Check package manager lock
        ↓
Inspect package state
```

---

## Related Topics

- `apt.md`
- `dpkg.md`
- `dnf.md`
- `rpm.md`
- `repositories.md`

---

## Conclusion

Most package-management failures fall into four categories:

```text
Repository
Dependency
Lock
Metadata
```

Start with the high-level package manager:

```text
APT
or
DNF
```

and only move to `dpkg` or `rpm` when lower-level inspection is needed.