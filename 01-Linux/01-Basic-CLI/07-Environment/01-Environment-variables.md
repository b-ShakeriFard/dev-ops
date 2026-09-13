# 🌍 Environment Variables

> Storing configuration and session information inside the shell environment.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Common Variables](#common-variables)
- [Creating Variables](#creating-variables)
- [Reading Variables](#reading-variables)
- [Shell vs Environment Variables](#shell-vs-environment-variables)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `VAR=value` | Create shell variable |
| `echo "$VAR"` | Read variable |
| `export VAR=value` | Export environment variable |
| `unset VAR` | Remove variable |
| `env` | Show environment |
| `printenv VAR` | Show one environment variable |

---

## Overview

Variables store values inside the shell.

Example:

```bash
APP_ENV="production"
```

Read it:

```bash
echo "$APP_ENV"
```

Output:

```text
production
```

Linux uses many variables automatically to describe the current session.

---

## Common Variables

Common examples:

```text
HOME  → home directory
USER  → current user
SHELL → login shell
PWD   → current directory
PATH  → command search path
LANG  → locale/language settings
```

Check them:

```bash
echo "$HOME"
echo "$USER"
echo "$SHELL"
echo "$PWD"
echo "$PATH"
```

---

## Creating Variables

Create:

```bash
NAME="alice"
```

Important:

```text
No spaces around =
```

Correct:

```bash
PORT=8080
```

Wrong:

```bash
PORT = 8080
```

---

## Reading Variables

Use:

```bash
$VARIABLE
```

Example:

```bash
echo "$NAME"
```

Braces are useful when combining text:

```bash
FILE="${NAME}_backup.txt"
```

Without braces:

```bash
"$NAME_backup"
```

Bash may interpret this as a different variable name.

---

## Shell vs Environment Variables

A shell variable:

```bash
APP="nginx"
```

exists only in the current shell.

Test:

```bash
bash -c 'echo "$APP"'
```

The child shell normally sees nothing.

Export it:

```bash
export APP
```

Now:

```bash
bash -c 'echo "$APP"'
```

can access it.

---

## Practical Examples

Store a directory:

```bash
BACKUP_DIR="/backup"
```

Use it:

```bash
mkdir -p "$BACKUP_DIR"
```

Create application setting:

```bash
export APP_ENV="production"
```

Check:

```bash
printenv APP_ENV
```

Remove it:

```bash
unset APP_ENV
```

---

## Common Pitfalls

### Forgetting Quotes

Prefer:

```bash
echo "$VAR"
```

instead of:

```bash
echo $VAR
```

### Assuming Variables Are Permanent

A variable created in the shell usually disappears when the session ends.

Persistent variables normally belong in shell startup files.

### Confusing Shell and Environment Variables

Only exported variables are inherited by child processes.

---

## Related Topics

- `env-printenv.md`
- `export.md`
- `path.md`
- `shell-startup-files.md`

---

## Conclusion

The basic pattern is:

```bash
VAR=value
echo "$VAR"
export VAR
```

The key concept is understanding when a value exists only in the current shell and when it is inherited by child processes.