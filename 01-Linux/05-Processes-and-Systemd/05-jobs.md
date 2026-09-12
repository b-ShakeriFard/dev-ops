# 🎛️ Shell Jobs

> Managing foreground and background processes from the shell.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Foreground and Background](#foreground-and-background)
- [Job Control](#job-control)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command / Key | Purpose |
|---|---|
| `COMMAND &` | Start command in background |
| `jobs` | List shell jobs |
| `Ctrl+Z` | Suspend foreground job |
| `bg` | Resume suspended job in background |
| `fg` | Bring job to foreground |
| `fg %1` | Bring job 1 to foreground |
| `kill %1` | Signal job 1 |
| `nohup COMMAND &` | Keep command running after logout |

---

## Overview

A shell can run commands in either:

```text
Foreground
or
Background
```

Foreground processes interact directly with the terminal.

Example:

```bash
ping example.com
```

The shell waits until the command finishes.

---

## Foreground and Background

Run in background:

```bash
ping example.com &
```

Example output:

```text
[1] 2456
```

Where:

```text
1    → Job ID
2456 → PID
```

Check jobs:

```bash
jobs
```

---

## Job Control

Suspend the current foreground job:

```text
Ctrl+Z
```

Example:

```text
[1]+ Stopped ping example.com
```

Resume in background:

```bash
bg %1
```

Bring back to foreground:

```bash
fg %1
```

Terminate the job:

```bash
kill %1
```

---

## Practical Examples

Start a long command:

```bash
sleep 1000 &
```

Check:

```bash
jobs
```

Bring it forward:

```bash
fg %1
```

Suspend:

```text
Ctrl+Z
```

Continue in background:

```bash
bg %1
```

---

## Keep a Process Running After Logout

A normal background process may terminate when the shell exits.

Use:

```bash
nohup ./script.sh &
```

Output is commonly written to:

```text
nohup.out
```

For long-running production services, use `systemd` rather than shell job control.

---

## Common Pitfalls

### Job ID vs PID

These are different:

```text
%1   → shell Job ID
2456 → system PID
```

Example:

```bash
kill %1
```

versus:

```bash
kill 2456
```

### Background Does Not Mean Persistent

This:

```bash
command &
```

does not guarantee the process survives logout.

Use `nohup`, a terminal multiplexer, or preferably `systemd` for persistent services.

---

## Related Topics

- `processes.md`
- `kill.md`
- `signals.md`
- `systemd.md`

---

## Conclusion

Shell job control lets you move commands between:

```text
Foreground
   ↕
Background
```

The essential commands are:

```bash
jobs
fg
bg
```

Use job control for interactive shell work and `systemd` for managed services.