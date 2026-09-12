# 📦 Bash Variables

> Storing and reusing values inside shell scripts.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Variables](#creating-variables)
- [Using Variables](#using-variables)
- [Environment Variables](#environment-variables)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `NAME=value` | Create variable |
| `$NAME` | Read variable |
| `${NAME}` | Read variable safely |
| `export NAME=value` | Create environment variable |
| `unset NAME` | Remove variable |
| `env` | Show environment variables |
| `printenv NAME` | Show one environment variable |

---

## Overview

Variables store values that can be reused later.

Example:

```bash
SERVER="web01"
```

Use it:

```bash
echo "$SERVER"
```

Output:

```text
web01
```

---

## Creating Variables

Syntax:

```bash
NAME=value
```

Example:

```bash
USER_NAME="alice"
PORT=8080
```

Important:

```text
No spaces around =
```

Wrong:

```bash
PORT = 8080
```

Correct:

```bash
PORT=8080
```

---

## Using Variables

Reference with:

```bash
$VARIABLE
```

Example:

```bash
HOST="server01"

echo "Connecting to $HOST"
```

Using braces is often clearer:

```bash
echo "${HOST}_backup"
```

Without braces:

```bash
echo "$HOST_backup"
```

Bash may interpret that as a different variable name.

---

## Environment Variables

Shell variables normally belong to the current shell.

Export one:

```bash
export APP_ENV="production"
```

Now child processes can inherit it.

Check:

```bash
printenv APP_ENV
```

Common environment variables include:

```text
PATH
HOME
USER
SHELL
PWD
```

Example:

```bash
echo "$HOME"
```

---

## Practical Examples

Store application information:

```bash
APP="nginx"
PORT=80

echo "$APP listens on port $PORT"
```

Build a path:

```bash
BACKUP_DIR="/backup"
DATE=$(date +%F)

FILE="${BACKUP_DIR}/backup-${DATE}.tar.gz"

echo "$FILE"
```

---

## Common Pitfalls

### Missing Quotes

Prefer:

```bash
echo "$NAME"
```

instead of:

```bash
echo $NAME
```

Quoting protects spaces and special characters.

### Single vs Double Quotes

Double quotes expand variables:

```bash
echo "$USER"
```

Single quotes do not:

```bash
echo '$USER'
```

Output:

```text
$USER
```

---

## Related Topics

- `input-output.md`
- `arguments.md`
- `command-substitution.md`
- `debugging.md`

---

## Conclusion

Variables make scripts reusable and easier to maintain.

The essential pattern is:

```bash
NAME=value
echo "$NAME"
```

Use `export` when the variable must also be available to child processes.