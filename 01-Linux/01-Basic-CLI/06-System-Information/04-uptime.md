# ⏱️ uptime

> Checking how long a Linux system has been running and viewing its load average.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Understanding Load Average](#understanding-load-average)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `uptime` | Show uptime and load average |
| `uptime -p` | Show uptime in human-readable form |
| `uptime -s` | Show system start time |
| `cat /proc/uptime` | Raw uptime data |
| `nproc` | Show number of CPUs |

---

## Overview

`uptime` gives a quick summary of system activity.

Example:

```bash
uptime
```

Possible output:

```text
19:40:01 up 12 days, 3:14, 2 users, load average: 0.42, 0.55, 0.60
```

This tells you:

```text
Current time
System uptime
Logged-in users
Load average
```

---

## Basic Usage

Run:

```bash
uptime
```

Human-readable uptime:

```bash
uptime -p
```

Example:

```text
up 12 days, 3 hours, 14 minutes
```

Show when the system booted:

```bash
uptime -s
```

Example:

```text
2026-09-01 16:26:03
```

---

## Understanding Load Average

The three numbers represent:

```text
1 minute
5 minutes
15 minutes
```

Example:

```text
load average: 1.20, 0.80, 0.60
```

Linux load includes tasks that are:

```text
Running
Waiting for CPU
Waiting in uninterruptible I/O
```

Load is not the same as CPU percentage.

---

## Compare Load with CPU Count

Check CPUs:

```bash
nproc
```

Suppose:

```text
CPUs = 4
Load = 4.0
```

That suggests the system is roughly fully occupied.

If load is:

```text
8.0
```

on a 4-CPU system, work may be waiting.

Always investigate further with:

```bash
top
vmstat 1
```

---

## Practical Examples

Quick server check:

```bash
hostname
uptime
```

Show boot time:

```bash
uptime -s
```

Raw uptime:

```bash
cat /proc/uptime
```

Example:

```text
1058400.44 927500.12
```

The first value is seconds since boot.

---

## Common Pitfalls

### Treating Load as CPU Percentage

A load of:

```text
4.0
```

does not mean:

```text
4%
```

### Ignoring CPU Count

A load of `4` is high on a 2-core server but may be normal on a 16-core system.

### Assuming High Load Means High CPU

High load can also come from:

```text
Disk I/O
Blocked processes
Storage problems
```

---

## Related Topics

- `hostname-uname.md`
- `whoami-id.md`
- `../../../09-Logs-Monitoring/uptime-load.md`
- `../../../09-Logs-Monitoring/cpu-memory.md`

---

## Conclusion

For a quick system health check:

```bash
uptime
nproc
```

`uptime` gives immediate context about how long the system has been running and whether overall load may deserve further investigation.