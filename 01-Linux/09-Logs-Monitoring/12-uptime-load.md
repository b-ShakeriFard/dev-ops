# ⏱️ Uptime & Load Average

> Understanding system uptime, load average, and whether a Linux host is under pressure.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [uptime](#uptime)
- [Load Average](#load-average)
- [Interpreting Load](#interpreting-load)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `uptime` | Show uptime and load average |
| `cat /proc/loadavg` | Show raw load values |
| `nproc` | Show CPU count |
| `top` | View load and CPU activity |
| `w` | Show users, uptime, and load |
| `vmstat 1` | Observe system pressure |

---

## Overview

`uptime` gives a quick summary of system activity.

Example:

```bash
uptime
```

Output may look like:

```text
03:20:15 up 12 days, 4:32, 2 users, load average: 0.42, 0.55, 0.60
```

This tells you:

```text
Current time
System uptime
Logged-in users
Load averages
```

---

## uptime

Run:

```bash
uptime
```

You can also use:

```bash
w
```

which shows similar load information plus logged-in users and their activity.

---

## Load Average

Linux shows three load values:

```text
1 minute
5 minutes
15 minutes
```

Example:

```text
load average: 1.20, 0.90, 0.70
```

Load includes tasks that are:

```text
Running on CPU
Waiting for CPU
Waiting in uninterruptible I/O
```

So load is not the same thing as CPU percentage.

---

## Interpreting Load

Check CPU count:

```bash
nproc
```

Suppose:

```text
CPUs = 4
Load = 4.0
```

That roughly means the available execution capacity is fully occupied.

A load of:

```text
8.0
```

on a 4-CPU system suggests work is waiting.

But load must always be interpreted with other metrics.

Check:

```bash
top
vmstat 1
```

---

## Practical Examples

Show raw load:

```bash
cat /proc/loadavg
```

Compare load with CPU count:

```bash
uptime
nproc
```

High load with high CPU usage may indicate CPU pressure.

High load with low CPU usage may indicate I/O waits or blocked processes.

Check:

```bash
vmstat 1
```

and:

```bash
ps -eo pid,stat,cmd
```

---

## Common Pitfalls

### Treating Load as CPU Percentage

Load:

```text
4.0
```

does not mean:

```text
4%
```

It is a queue-like measure of runnable and certain waiting tasks.

### Ignoring CPU Count

A load of `4` means something very different on:

```text
2 CPUs
vs
32 CPUs
```

---

## Related Topics

- `cpu-memory.md`
- `process-monitoring.md`
- `disk-monitoring.md`
- `monitoring-basics.md`
- `troubleshooting.md`

---

## Conclusion

Always interpret load together with:

```bash
nproc
top
vmstat
```

The key question is not just:

```text
Is load high?
```

but:

```text
Why is load high?
```