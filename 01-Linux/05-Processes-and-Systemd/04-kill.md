# 🛑 kill - Process Control

> Sending signals to Linux processes to stop, reload, or terminate them.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Common Signals](#common-signals)
- [Practical Examples](#practical-examples)
- [Graceful vs Forceful Termination](#graceful-vs-forceful-termination)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `kill PID` | Send SIGTERM |
| `kill -15 PID` | Graceful termination |
| `kill -9 PID` | Force termination |
| `kill -HUP PID` | Send SIGHUP |
| `pkill NAME` | Signal process by name |
| `killall NAME` | Signal matching process names |
| `pgrep NAME` | Find PID by name |

---

## Overview

Despite its name, `kill` does not always kill a process.

It sends a **signal**.

Default:

```bash
kill 1234
```

means:

```text
Send SIGTERM to PID 1234
```

The process can then shut down cleanly.

---

## Common Signals

| Signal | Number | Purpose |
|---|---:|---|
| `SIGHUP` | 1 | Reload/reinitialize |
| `SIGINT` | 2 | Interrupt |
| `SIGTERM` | 15 | Graceful termination |
| `SIGKILL` | 9 | Immediate termination |

List signals:

```bash
kill -l
```

---

## Practical Examples

Find a process:

```bash
pgrep -a nginx
```

Stop gracefully:

```bash
kill 1234
```

Equivalent:

```bash
kill -15 1234
```

Force stop:

```bash
kill -9 1234
```

Signal by process name:

```bash
pkill nginx
```

Reload a process that supports SIGHUP:

```bash
kill -HUP 1234
```

---

## Graceful vs Forceful Termination

Preferred:

```text
SIGTERM
   ↓
Process cleans up
   ↓
Process exits
```

Last resort:

```text
SIGKILL
   ↓
Kernel terminates immediately
```

`SIGKILL` cannot be caught or ignored by the process.

---

## Common Pitfalls

### Using `kill -9` Too Quickly

This may prevent:

- Cleanup
- File flushing
- Graceful connection shutdown

Try first:

```bash
kill PID
```

Then use:

```bash
kill -9 PID
```

only if needed.

### Killing the Wrong PID

Verify first:

```bash
ps -p PID -f
```

---

## Related Topics

- `processes.md`
- `ps.md`
- `top.md`
- `signals.md`
- `systemctl.md`

---

## Conclusion

The key rule is:

```text
SIGTERM first
SIGKILL last
```

Use `kill` to send signals deliberately, not just to force processes to stop.