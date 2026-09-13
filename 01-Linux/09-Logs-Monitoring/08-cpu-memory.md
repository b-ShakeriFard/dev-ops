# 🧠 CPU & Memory Monitoring

> Checking processor load, memory usage, and resource pressure on Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [CPU Monitoring](#cpu-monitoring)
- [Memory Monitoring](#memory-monitoring)
- [vmstat](#vmstat)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `top` | Live CPU and memory view |
| `free -h` | Memory summary |
| `vmstat 1` | CPU/memory activity every second |
| `ps aux --sort=-%cpu` | Sort processes by CPU |
| `ps aux --sort=-%mem` | Sort processes by memory |
| `uptime` | Show load average |
| `nproc` | Show CPU count |

---

## Overview

CPU and memory problems often cause:

```text
Slow applications
High load
Delayed responses
Process crashes
System instability
```

The goal is to determine:

```text
Is there resource pressure?
        ↓
Which process is responsible?
```

---

## CPU Monitoring

Start with:

```bash
top
```

Important CPU values include:

```text
us → user-space CPU
sy → kernel CPU
id → idle CPU
wa → I/O wait
```

Find top CPU consumers:

```bash
ps aux --sort=-%cpu | head
```

Check CPU count:

```bash
nproc
```

---

## Memory Monitoring

Check memory:

```bash
free -h
```

Typical output includes:

```text
total
used
free
buff/cache
available
swap
```

The most useful value is often:

```text
available
```

because Linux intentionally uses unused RAM for caching.

Find memory-heavy processes:

```bash
ps aux --sort=-%mem | head
```

---

## vmstat

`vmstat` provides a quick resource overview.

Run:

```bash
vmstat 1
```

Useful columns:

```text
r  → runnable processes
si → swap-in
so → swap-out
us → user CPU
sy → system CPU
id → idle CPU
wa → I/O wait
```

Frequent `si` and `so` activity may indicate memory pressure.

---

## Practical Examples

### Find High CPU Process

```bash
ps -eo pid,user,%cpu,%mem,cmd \
--sort=-%cpu | head
```

### Find High Memory Process

```bash
ps -eo pid,user,%mem,%cpu,cmd \
--sort=-%mem | head
```

### Watch Resource Changes

```bash
vmstat 2
```

This updates every two seconds.

---

## Common Pitfalls

### Assuming High Used RAM Is Bad

Linux uses memory for cache.

Check:

```text
available
```

before assuming the system is running out of memory.

### Ignoring I/O Wait

High:

```text
wa
```

may mean CPU is waiting on storage rather than doing useful work.

---

## Related Topics

- `monitoring-basics.md`
- `process-monitoring.md`
- `disk-monitoring.md`
- `uptime-load.md`
- `troubleshooting.md`

---

## Conclusion

For CPU and memory troubleshooting, start with:

```bash
top
free -h
vmstat 1
```

Then identify the processes responsible with `ps`.