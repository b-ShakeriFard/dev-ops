# 📊 top - Live Process Monitoring

> Monitoring CPU, memory, load, and running processes in real time.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Understanding the Header](#understanding-the-header)
- [Useful Interactive Keys](#useful-interactive-keys)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Key / Command | Purpose |
|---|---|
| `top` | Start live process monitor |
| `P` | Sort by CPU |
| `M` | Sort by memory |
| `1` | Show individual CPU cores |
| `k` | Kill a process |
| `r` | Change process priority |
| `u` | Filter by user |
| `q` | Quit |

---

## Overview

`top` provides a continuously updating view of system activity.

It shows:

```text
CPU usage
Memory usage
Load average
Process states
Running processes
```

Start:

```bash
top
```

---

## Understanding the Header

Example:

```text
load average: 0.50, 0.40, 0.30
```

These represent system load over roughly:

```text
1 minute
5 minutes
15 minutes
```

CPU line:

```text
us  → user processes
sy  → kernel/system
id  → idle
wa  → I/O wait
```

High `wa` may indicate storage bottlenecks.

---

## Memory Information

`top` displays:

```text
total
free
used
buff/cache
```

Linux intentionally uses unused memory for caching.

So:

```text
low "free" memory
```

does not automatically mean there is a problem.

---

## Process Columns

Important columns:

| Column | Meaning |
|---|---|
| `PID` | Process ID |
| `USER` | Owner |
| `%CPU` | CPU usage |
| `%MEM` | Memory usage |
| `RES` | Physical memory used |
| `STAT` | Process state |
| `COMMAND` | Process name |

---

## Useful Interactive Keys

Sort by CPU:

```text
P
```

Sort by memory:

```text
M
```

Show each CPU core:

```text
1
```

Filter by user:

```text
u
```

Enter:

```text
nginx
```

Kill a process:

```text
k
```

Then enter its PID and signal.

Quit:

```text
q
```

---

## Practical Examples

Find a CPU-heavy process:

```bash
top
```

Then press:

```text
P
```

Find memory-heavy processes:

```text
M
```

Show all CPU cores:

```text
1
```

This is useful when one core is saturated while others remain mostly idle.

---

## top vs ps

```text
ps
→ snapshot

top
→ live monitoring
```

Use `ps` when you need precise scripted output.

Use `top` when watching system behavior interactively.

---

## Related Topics

- `processes.md`
- `ps.md`
- `kill.md`
- `signals.md`
- `../09-Logs-Monitoring/`

---

## Conclusion

`top` is one of the fastest ways to answer:

```text
What is consuming CPU?

What is consuming memory?

Is the system waiting on I/O?

Which process is causing it?
```

For live Linux troubleshooting, it is one of the first commands worth running.