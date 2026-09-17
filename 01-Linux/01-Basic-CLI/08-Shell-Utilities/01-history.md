# 🕘 history

> Viewing, searching, and reusing previously executed shell commands.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Viewing History](#viewing-history)
- [Searching History](#searching-history)
- [Reusing Commands](#reusing-commands)
- [History Files](#history-files)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command / Shortcut | Purpose |
|---|---|
| `history` | Show command history |
| `history 20` | Show last 20 commands |
| `history \| grep ssh` | Search history |
| `!!` | Repeat previous command |
| `!123` | Run history entry 123 |
| `!ssh` | Run most recent command starting with `ssh` |
| `Ctrl+R` | Reverse interactive search |
| `history -c` | Clear current history list |

---

## Overview

The shell keeps a history of previously executed commands.

Example:

```bash
history
```

Output may look like:

```text
101  ls -lah
102  cd /var/log
103  journalctl -u nginx
104  systemctl status nginx
```

This is useful for recalling commands without typing them again.

---

## Viewing History

Show all available history:

```bash
history
```

Show the most recent 20 entries:

```bash
history 20
```

Search for previous SSH commands:

```bash
history | grep ssh
```

---

## Searching History

One of the fastest methods is:

```text
Ctrl+R
```

Then start typing part of a previous command.

Example:

```text
(reverse-i-search)`nginx':
```

Press:

```text
Ctrl+R
```

again to move through older matches.

Press:

```text
Enter
```

to execute the selected command.

---

## Reusing Commands

Repeat the previous command:

```bash
!!
```

Example:

```bash
apt update
```

If permission was denied:

```bash
sudo !!
```

This becomes:

```bash
sudo apt update
```

Run a specific history entry:

```bash
!123
```

Run the most recent command beginning with `ssh`:

```bash
!ssh
```

---

## History Files

Bash commonly stores persistent history in:

```text
~/.bash_history
```

Check:

```bash
echo "$HISTFILE"
```

Useful variables include:

```text
HISTFILE
HISTSIZE
HISTFILESIZE
```

---

## Practical Examples

Find Kubernetes commands:

```bash
history | grep kubectl
```

Find package installation commands:

```bash
history | grep install
```

Show recent commands:

```bash
history | tail -20
```

---

## Common Pitfalls

### Sensitive Commands

Avoid putting secrets directly on the command line.

Example:

```bash
command --password secret123
```

may appear in history.

Prefer secure prompts, files, or secret-management mechanisms.

### `!!` Executes Immediately

Always know what the previous command was before using:

```bash
sudo !!
```

### History May Not Be Written Immediately

Different shell sessions may write history when they exit.

Behavior can also be controlled by shell configuration.

---

## Related Topics

- `alias-unalias.md`
- `echo-printf.md`
- `tee.md`
- `../07-Environment/shell-startup-files.md`

---

## Conclusion

The most useful history techniques are:

```bash
history
history | grep TEXT
```

and:

```text
Ctrl+R
```

Once `Ctrl+R` becomes muscle memory, navigating previous Linux commands becomes dramatically faster.