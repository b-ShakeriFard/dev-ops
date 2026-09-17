# 🧹 clear

> Clearing the terminal display without ending the current shell session.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Keyboard Shortcut](#keyboard-shortcut)
- [clear vs reset](#clear-vs-reset)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command / Shortcut | Purpose |
|---|---|
| `clear` | Clear terminal display |
| `Ctrl+L` | Common shortcut for clear |
| `reset` | Reinitialize a broken terminal |
| `history` | Previous commands are still available |

---

## Overview

`clear` removes the visible contents of the terminal screen.

Run:

```bash
clear
```

The shell itself continues running.

Your:

```text
Working directory
Environment variables
Aliases
Command history
Processes
```

remain unchanged.

---

## Basic Usage

Suppose your terminal contains lots of output:

```bash
ls -lah
ps aux
df -h
```

Run:

```bash
clear
```

The terminal becomes visually clean.

This is useful before:

```text
Running a demonstration
Starting troubleshooting
Taking screenshots
Reading fresh output
```

---

## Keyboard Shortcut

In many shells and terminal environments:

```text
Ctrl+L
```

clears or redraws the screen.

It is often faster than typing:

```bash
clear
```

---

## clear vs reset

`clear` simply clears the visible terminal.

```bash
clear
```

Use `reset` when the terminal itself has become corrupted.

Example symptoms:

```text
Unreadable characters
Broken formatting
Strange terminal behavior
Input not displaying correctly
```

Then try:

```bash
reset
```

`reset` performs a more complete terminal reinitialization.

---

## Practical Examples

Clear before monitoring:

```bash
clear
top
```

Clear before reading logs:

```bash
clear
journalctl -u nginx -f
```

Clear and show system information:

```bash
clear
hostname
uptime
df -h
```

---

## Common Pitfalls

### Assuming History Is Deleted

`clear` does **not** remove command history.

Check:

```bash
history
```

Previous commands remain available.

### Assuming Output Is Permanently Gone

Many terminal emulators retain scrollback even after:

```bash
clear
```

You may still be able to scroll upward.

### Using reset Unnecessarily

For normal cleanup:

```bash
clear
```

is enough.

Use:

```bash
reset
```

when the terminal is actually malfunctioning.

---

## Related Topics

- `history.md`
- `alias-unalias.md`
- `echo-printf.md`
- `tee.md`

---

## Conclusion

For normal terminal cleanup:

```bash
clear
```

or:

```text
Ctrl+L
```

For a corrupted terminal:

```bash
reset
```

`clear` changes the display, not the shell session itself.