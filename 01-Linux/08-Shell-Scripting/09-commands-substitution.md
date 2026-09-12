# 🔄 Bash Command Substitution

> Capturing the output of commands and storing it in variables.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Syntax](#basic-syntax)
- [Using Command Output](#using-command-output)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `$(command)` | Capture command output |
| `VAR=$(command)` | Store output in variable |
| `"$(command)"` | Safely use command output |
| `` `command` `` | Older syntax |

---

## Overview

Command substitution runs a command and replaces it with its output.

Example:

```bash
HOST=$(hostname)
```

Now:

```bash
echo "$HOST"
```

prints the hostname returned by:

```bash
hostname
```

---

## Basic Syntax

Preferred syntax:

```bash
$(command)
```

Example:

```bash
DATE=$(date)
```

Use:

```bash
echo "$DATE"
```

Older Bash syntax also exists:

```bash
DATE=`date`
```

But `$(...)` is easier to read and nest.

---

## Using Command Output

You can use command substitution directly:

```bash
echo "Current user: $(whoami)"
```

Or store the result:

```bash
KERNEL=$(uname -r)

echo "Kernel: $KERNEL"
```

---

## Practical Examples

### Create Timestamped Backup Name

```bash
DATE=$(date +%F)

BACKUP="backup-${DATE}.tar.gz"

echo "$BACKUP"
```

Output:

```text
backup-2026-09-13.tar.gz
```

---

### Store Disk Usage

```bash
USAGE=$(df -h / | awk 'NR==2 {print $5}')

echo "Root usage: $USAGE"
```

---

### Store Process Count

```bash
COUNT=$(ps -e --no-headers | wc -l)

echo "Processes: $COUNT"
```

---

### Use Inside a Message

```bash
echo "Server $(hostname) checked at $(date '+%H:%M:%S')"
```

---

## Nested Command Substitution

Command substitution can be nested:

```bash
echo "$(basename "$(pwd)")"
```

This is much cleaner than nested backticks.

---

## Common Pitfalls

### Losing Newlines

Command substitution removes trailing newline characters.

For most scripting tasks, this is fine.

### Unquoted Output

Prefer:

```bash
VALUE="$(command)"
```

instead of:

```bash
VALUE=$(command)
```

when the output may contain spaces or special characters.

### Assuming Success

Command substitution captures output, not success.

Check exit status separately when needed:

```bash
OUTPUT=$(command)

if [ "$?" -eq 0 ]; then
    echo "Success"
fi
```

---

## Related Topics

- `variables.md`
- `exit-codes.md`
- `pipes-redirection.md`
- `practical-scripts.md`

---

## Conclusion

Command substitution connects Linux commands with shell variables.

The essential pattern is:

```bash
VARIABLE=$(command)
```

It is one of the most useful techniques in practical Bash automation.