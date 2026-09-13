# 📈 Linux Monitoring Basics

> Understanding the main system resources you should watch on a Linux server.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [What to Monitor](#what-to-monitor)
- [Key Commands](#key-commands)
- [Baseline vs Problem](#baseline-vs-problem)
- [Practical Workflow](#practical-workflow)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Resource | Command |
|---|---|
| CPU | `top` |
| Memory | `free -h` |
| Processes | `ps aux` |
| Disk usage | `df -h` |
| Directory size | `du -sh PATH` |
| Load average | `uptime` |
| Network sockets | `ss -tulpn` |
| Kernel events | `dmesg` |

---

## Overview

Monitoring means observing the current condition of a system.

A useful mental model is:

```text
CPU
Memory
Disk
Processes
Network
Load
```

If one of these becomes unhealthy, application performance may degrade.

---

## What to Monitor

### CPU

Questions:

```text
Is CPU usage high?
Which process is using it?
Is load increasing?
```

Useful command:

```bash
top
```

---

### Memory

Check:

```bash
free -h
```

Important values include:

```text
available
used
swap
```

High memory usage alone is not always a problem because Linux also uses memory for cache.

---

### Disk

Filesystem usage:

```bash
df -h
```

Directory usage:

```bash
du -sh /var/*
```

A full filesystem can break applications, logging, and package management.

---

### Processes

Check:

```bash
ps aux
```

or:

```bash
top
```

Look for:

```text
High CPU
High memory
Unexpected processes
Zombie processes
```

---

### Network

Check listening sockets:

```bash
ss -tulpn
```

Check interfaces:

```bash
ip addr
```

---

## Key Commands

A quick server health check:

```bash
uptime
free -h
df -h
top
ss -tulpn
```

These commands give a fast overview of system health.

---

## Baseline vs Problem

Monitoring works best when you know what normal looks like.

Example:

```text
Normal CPU: 20%
Normal load: 1.0
Normal disk: 55%
```

Then suddenly:

```text
CPU: 95%
Load: 12
Disk: 98%
```

The change is often more useful than the raw number itself.

---

## Practical Workflow

```text
Something feels slow
      ↓
Check uptime/load
      ↓
Check CPU
      ↓
Check memory
      ↓
Check disk
      ↓
Check processes
      ↓
Check network
```

Then use logs to find the cause.

---

## Related Topics

- `cpu-memory.md`
- `disk-monitoring.md`
- `process-monitoring.md`
- `network-monitoring.md`
- `uptime-load.md`
- `troubleshooting.md`

---

## Conclusion

Linux monitoring is about answering:

```text
What resource is under pressure?
Which process or service is responsible?
Is this normal for this system?
```

Start broad, then narrow down to the affected component.