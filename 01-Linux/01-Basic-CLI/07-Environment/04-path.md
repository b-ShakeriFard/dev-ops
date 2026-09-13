# 🛣️ PATH

> Understanding how Linux finds commands and executables.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Viewing PATH](#viewing-path)
- [How Command Lookup Works](#how-command-lookup-works)
- [Adding Directories to PATH](#adding-directories-to-path)
- [Finding Commands](#finding-commands)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `echo "$PATH"` | Show PATH |
| `printenv PATH` | Show exported PATH |
| `which COMMAND` | Show command path |
| `command -v COMMAND` | Find how shell resolves command |
| `type COMMAND` | Show command type |
| `export PATH="$PATH:/opt/bin"` | Add directory temporarily |

---

## Overview

`PATH` is an environment variable containing directories where the shell looks for commands.

Example:

```bash
echo "$PATH"
```

Possible output:

```text
/usr/local/bin:/usr/bin:/bin
```

Directories are separated by:

```text
:
```

---

## Viewing PATH

Run:

```bash
echo "$PATH"
```

For easier reading:

```bash
echo "$PATH" | tr ':' '\n'
```

Example:

```text
/usr/local/bin
/usr/bin
/bin
```

---

## How Command Lookup Works

Suppose you run:

```bash
ls
```

The shell searches PATH directories in order:

```text
/usr/local/bin
      ↓
/usr/bin
      ↓
/bin
```

Once it finds an executable named `ls`, it runs it.

This is why you normally do not need:

```bash
/bin/ls
```

---

## Adding Directories to PATH

Temporarily add a directory:

```bash
export PATH="$PATH:/opt/myapp/bin"
```

Now executables in:

```text
/opt/myapp/bin
```

can be run by name.

Prepend instead:

```bash
export PATH="/opt/myapp/bin:$PATH"
```

Prepending gives that directory higher search priority.

---

## Finding Commands

Recommended:

```bash
command -v python3
```

Example:

```text
/usr/bin/python3
```

Another option:

```bash
which python3
```

Check command type:

```bash
type cd
```

Output may show:

```text
cd is a shell builtin
```

---

## Practical Examples

Suppose:

```text
/home/alice/bin/backup
```

exists and is executable.

Add its directory:

```bash
export PATH="$PATH:$HOME/bin"
```

Now:

```bash
backup
```

can run directly.

Check resolution:

```bash
command -v backup
```

---

## Common Pitfalls

### Overwriting PATH

Dangerous:

```bash
export PATH="/opt/bin"
```

This removes normal system paths.

Safer:

```bash
export PATH="$PATH:/opt/bin"
```

### Current Directory Is Usually Not in PATH

If you create:

```text
script.sh
```

you may need:

```bash
./script.sh
```

rather than:

```bash
script.sh
```

### Command Still Not Found

Check:

```bash
command -v COMMAND
```

Then verify:

```text
Correct PATH
Executable permission
Correct filename
Correct architecture/interpreter
```

---

## Related Topics

- `environment-variables.md`
- `export.md`
- `source.md`
- `shell-startup-files.md`

---

## Conclusion

`PATH` answers:

```text
Where should the shell look for commands?
```

The key pattern is:

```bash
export PATH="$PATH:/new/directory"
```

Understanding PATH solves many common `command not found` problems.