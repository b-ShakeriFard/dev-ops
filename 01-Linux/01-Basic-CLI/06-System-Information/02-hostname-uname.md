# 🖥️ hostname & uname

> Identifying the machine, kernel, architecture, and operating environment.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using hostname](#using-hostname)
- [Using uname](#using-uname)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `hostname` | Show system hostname |
| `hostname -f` | Show FQDN when configured |
| `hostname -I` | Show host IP addresses |
| `uname` | Show kernel name |
| `uname -r` | Show kernel release |
| `uname -m` | Show machine architecture |
| `uname -a` | Show detailed system information |

---

## Overview

`hostname` identifies the machine.

`uname` identifies the running kernel and hardware platform.

Together, they answer:

```text
Which machine is this?
What kernel is running?
What architecture is it?
```

---

## Using hostname

Show hostname:

```bash
hostname
```

Example:

```text
web01
```

Show fully qualified domain name:

```bash
hostname -f
```

Possible output:

```text
web01.example.com
```

Show IP addresses associated with the host:

```bash
hostname -I
```

---

## Using uname

Show kernel name:

```bash
uname
```

Typical output:

```text
Linux
```

Kernel release:

```bash
uname -r
```

Example:

```text
6.8.0-79-generic
```

Machine architecture:

```bash
uname -m
```

Common values:

```text
x86_64
aarch64
armv7l
```

Show everything:

```bash
uname -a
```

---

## Practical Examples

Check host and kernel:

```bash
hostname
uname -r
```

Check architecture before downloading software:

```bash
uname -m
```

Example logic:

```text
x86_64  → 64-bit Intel/AMD
aarch64 → 64-bit ARM
```

Quick server identification:

```bash
echo "Host: $(hostname)"
echo "Kernel: $(uname -r)"
echo "Arch: $(uname -m)"
```

---

## Common Pitfalls

### `uname` Is Not the Linux Distribution

This:

```bash
uname -a
```

shows kernel/system information.

To identify the distribution:

```bash
cat /etc/os-release
```

### Hostname vs FQDN

These may differ:

```bash
hostname
hostname -f
```

The FQDN depends on DNS and local hostname configuration.

### `hostname -I` May Show Several Addresses

A system may have:

```text
Multiple interfaces
Docker bridges
VPN addresses
IPv6 addresses
```

Use `ip addr` for a detailed network view.

---

## Related Topics

- `date-cal.md`
- `whoami-id.md`
- `uptime.md`
- `../../06-Networking/`

---

## Conclusion

For fast system identification, use:

```bash
hostname
uname -r
uname -m
```

Together, they provide the machine name, kernel version, and CPU architecture.