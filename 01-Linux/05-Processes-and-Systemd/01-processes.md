# ⚙️ Linux Processes

> Understanding how Linux runs programs and tracks their execution.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [PID and PPID](#pid-and-ppid)
- [Process States](#process-states)
- [Foreground vs Background](#foreground-vs-background)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ps` | Show processes |
| `ps aux` | Show detailed process list |
| `pgrep NAME` | Find PID by process name |
| `pidof NAME` | Find process ID |
| `top` | Monitor processes live |
| `kill PID` | Send signal to process |
| `jobs` | Show shell jobs |

---

## Overview

A process is a running instance of a program.

Example:

```text
Program
  ↓
Process
  ↓
PID
```

A single program can have multiple running processes.

Example:

```bash
ps aux | grep nginx
```

may show several nginx processes.

---

## PID and PPID

Every process has a unique:

```text
PID = Process ID
```

A process may also have a parent:

```text
PPID = Parent Process ID
```

Example:

```bash
ps -o pid,ppid,cmd
```

A shell may start a child process:

```text
bash
 ↓
python3
```

---

## Process States

Common states include:

| State | Meaning |
|---|---|
| `R` | Running |
| `S` | Sleeping |
| `D` | Uninterruptible sleep |
| `T` | Stopped |
| `Z` | Zombie |

Check:

```bash
ps aux
```

The `STAT` column shows the process state.

---

## Foreground vs Background

Foreground process:

```bash
ping example.com
```

Background process:

```bash
ping example.com &
```

Show jobs:

```bash
jobs
```

Bring job to foreground:

```bash
fg
```

Send process to background:

```bash
bg
```

---

## Practical Examples

Find nginx processes:

```bash
pgrep nginx
```

Show process tree:

```bash
ps -ef --forest
```

Check a specific PID:

```bash
ps -p 1234 -f
```

Show owner and resource usage:

```bash
ps aux
```

---

## Important Concept

Processes consume system resources:

```text
CPU
Memory
Files
Network sockets
```

This is why process management connects directly to system monitoring and troubleshooting.

---

## Related Topics

- `ps.md`
- `top.md`
- `kill.md`
- `jobs.md`
- `signals.md`

---

## Conclusion

Linux runs programs as processes.

The key identifiers are:

```text
PID  → process
PPID → parent process
```

Understanding processes is the foundation for service management, monitoring, and troubleshooting.