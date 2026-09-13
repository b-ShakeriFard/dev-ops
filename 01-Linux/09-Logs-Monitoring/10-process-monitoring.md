# ⚙️ Process Monitoring

> Inspecting running processes, resource usage, states, and process relationships.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [ps](#ps)
- [top](#top)
- [Process States](#process-states)
- [Finding Processes](#finding-processes)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ps aux` | Show running processes |
| `ps -ef` | Full process listing |
| `top` | Live process monitoring |
| `pgrep NAME` | Find process PID |
| `pgrep -a NAME` | PID and command |
| `pstree` | Show process hierarchy |
| `ps --sort=-%cpu` | Sort by CPU |
| `ps --sort=-%mem` | Sort by memory |

---

## Overview

Every running program in Linux is a process.

Monitoring processes helps answer:

```text
What is running?
Which process uses resources?
Is a process stuck?
Who started it?
What is its PID?
```

---

## ps

Show all processes:

```bash
ps aux
```

Another common format:

```bash
ps -ef
```

Useful fields include:

```text
PID   → Process ID
USER  → Owner
%CPU  → CPU usage
%MEM  → Memory usage
STAT  → Process state
CMD   → Command
```

---

## top

For live monitoring:

```bash
top
```

Useful keys inside `top`:

```text
P → sort by CPU
M → sort by memory
1 → show individual CPUs
k → kill process
q → quit
```

For most investigations, `top` is the fastest first step.

---

## Process States

Common states:

```text
R → Running
S → Sleeping
D → Uninterruptible sleep
T → Stopped
Z → Zombie
```

Check state:

```bash
ps -eo pid,stat,cmd
```

A `Z` process has exited but its parent has not yet collected its status.

---

## Finding Processes

Find by name:

```bash
pgrep nginx
```

Show PID and command:

```bash
pgrep -a nginx
```

Show hierarchy:

```bash
pstree
```

Inspect one PID:

```bash
ps -p 1234 -o pid,ppid,user,%cpu,%mem,stat,cmd
```

---

## Practical Examples

Top CPU consumers:

```bash
ps -eo pid,user,%cpu,%mem,cmd \
--sort=-%cpu | head
```

Top memory consumers:

```bash
ps -eo pid,user,%mem,%cpu,cmd \
--sort=-%mem | head
```

Find zombie processes:

```bash
ps -eo pid,ppid,stat,cmd | grep ' Z'
```

Monitor one process:

```bash
top -p 1234
```

---

## Common Pitfalls

### Killing Before Investigating

First inspect:

```bash
ps -p PID -o pid,ppid,user,stat,cmd
```

Then decide whether termination is appropriate.

### High CPU Does Not Always Mean a Problem

A busy process may be doing expected work.

Compare current behavior with the system's normal baseline.

---

## Related Topics

- `cpu-memory.md`
- `monitoring-basics.md`
- `uptime-load.md`
- `troubleshooting.md`

---

## Conclusion

A strong process-monitoring workflow is:

```text
top
 ↓
Identify PID
 ↓
ps / pgrep
 ↓
Inspect state and resources
 ↓
Take action if necessary
```

Process monitoring connects system resource usage to the actual application or service responsible.