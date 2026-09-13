# 🧠 dmesg

> Viewing kernel messages for hardware, drivers, boot events, and low-level system problems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Viewing Kernel Messages](#viewing-kernel-messages)
- [Filtering Output](#filtering-output)
- [Human-Readable Time](#human-readable-time)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `dmesg` | Show kernel ring buffer |
| `dmesg -T` | Human-readable timestamps |
| `dmesg -w` | Follow new kernel messages |
| `dmesg \| grep -i error` | Search for errors |
| `dmesg \| grep -i usb` | Check USB events |
| `dmesg \| grep -i disk` | Check disk-related messages |
| `journalctl -k` | View kernel logs via journal |

---

## Overview

`dmesg` displays messages produced by the Linux kernel.

These messages often include:

```text
Hardware detection
Device drivers
Disk problems
Network interfaces
USB devices
Kernel warnings
Boot events
```

This makes `dmesg` especially useful for low-level troubleshooting.

---

## Viewing Kernel Messages

Show kernel messages:

```bash
dmesg
```

The output may be large, so use:

```bash
dmesg | less
```

Show recent messages:

```bash
dmesg | tail
```

---

## Filtering Output

Search for errors:

```bash
dmesg | grep -i error
```

Warnings:

```bash
dmesg | grep -i warning
```

USB devices:

```bash
dmesg | grep -i usb
```

Network interfaces:

```bash
dmesg | grep -i eth
```

Storage:

```bash
dmesg | grep -Ei 'disk|sd[a-z]|nvme'
```

---

## Human-Readable Time

By default, timestamps are relative to boot.

Use:

```bash
dmesg -T
```

Example:

```text
[Sun Sep 13 03:20:01 2026] ...
```

This can make troubleshooting easier.

---

## Follow New Messages

Watch kernel events live:

```bash
dmesg -w
```

Useful when:

```text
Connecting USB device
Adding disk
Loading driver
Testing hardware
```

---

## Practical Examples

### Detect New USB Device

Run:

```bash
dmesg -w
```

Then plug in the device.

### Check Disk Errors

```bash
dmesg | grep -Ei 'I/O error|nvme|sd[a-z]'
```

### Check Network Driver

```bash
dmesg | grep -i firmware
```

or:

```bash
dmesg | grep -i ethernet
```

---

## Common Pitfalls

### Permission Denied

Some systems restrict access:

```bash
sudo dmesg
```

### dmesg Is Not Permanent Storage

The kernel ring buffer is limited and older messages may disappear.

For persistent kernel logs, use:

```bash
journalctl -k
```

---

## Related Topics

- `journalctl.md`
- `logs-overview.md`
- `disk-monitoring.md`
- `network-monitoring.md`
- `troubleshooting.md`

---

## Conclusion

Use `dmesg` when the problem appears to involve:

```text
Kernel
Hardware
Driver
Disk
Network interface
USB device
```

For low-level Linux troubleshooting, it is one of the most valuable diagnostic commands.