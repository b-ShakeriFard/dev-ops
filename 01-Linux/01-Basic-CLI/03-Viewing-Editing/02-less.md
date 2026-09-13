# 📖 less

> Viewing large text files interactively without loading everything into the terminal at once.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Navigation](#navigation)
- [Searching](#searching)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Key / Command | Purpose |
|---|---|
| `less FILE` | Open file |
| `Space` | Next page |
| `b` | Previous page |
| `↑ / ↓` | Move one line |
| `g` | Go to beginning |
| `G` | Go to end |
| `/text` | Search forward |
| `?text` | Search backward |
| `n` | Next match |
| `N` | Previous match |
| `q` | Quit |

---

## Overview

`less` is designed for reading long text files.

Example:

```bash
less /var/log/messages
```

Unlike:

```bash
cat file.txt
```

`less` provides an interactive viewer that lets you scroll, search, and jump around.

---

## Basic Usage

Open a file:

```bash
less file.txt
```

Open command output:

```bash
ps aux | less
```

Another common example:

```bash
dmesg | less
```

---

## Navigation

Move forward one page:

```text
Space
```

Move backward:

```text
b
```

Go to first line:

```text
g
```

Go to last line:

```text
G
```

Quit:

```text
q
```

---

## Searching

Search forward:

```text
/error
```

Press:

```text
n
```

for the next match.

Search backward:

```text
?error
```

Previous match:

```text
N
```

Searches are case-sensitive by default.

---

## Practical Examples

Inspect configuration:

```bash
less /etc/ssh/sshd_config
```

View a large log:

```bash
less /var/log/syslog
```

Start at the end of a file:

```bash
less +G application.log
```

Show line numbers:

```bash
less -N file.txt
```

Follow a growing file:

```bash
less +F application.log
```

Press:

```text
Ctrl+C
```

to stop following and return to normal navigation.

---

## Common Pitfalls

### Trying to Edit

`less` is primarily a viewer, not a text editor.

Use:

```bash
nano file.txt
```

or:

```bash
vi file.txt
```

to modify content.

### Forgetting How to Exit

Press:

```text
q
```

### Using cat for Huge Logs

Instead of:

```bash
cat huge.log
```

prefer:

```bash
less huge.log
```

---

## Related Topics

- `cat.md`
- `head-tail.md`
- `nano.md`
- `vi.md`

---

## Conclusion

`less` is the preferred tool for inspecting large files.

The essential keys are:

```text
Space → forward
b     → backward
/     → search
G     → end
q     → quit
```

For large logs and configuration files, `less` is one of the most useful Linux commands to know.