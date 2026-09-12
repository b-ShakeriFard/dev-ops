# 🚦 Bash Exit Codes

> Understanding command success, failure, and script return status.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Checking Exit Codes](#checking-exit-codes)
- [Using exit](#using-exit)
- [Using Exit Codes in Conditions](#using-exit-codes-in-conditions)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `$?` | Previous command exit code |
| `exit 0` | Successful script exit |
| `exit 1` | Generic failure |
| `command && next` | Run next if success |
| `command || next` | Run next if failure |

---

## Overview

Every Linux command returns an exit status.

Convention:

```text
0     → success
non-0 → failure
```

Example:

```bash
ls /etc/hosts
echo "$?"
```

If successful:

```text
0
```

If the command fails, Bash returns a non-zero value.

---

## Checking Exit Codes

Example:

```bash
ping -c 1 server01

echo "$?"
```

Important:

```bash
$?
```

only contains the status of the **most recently executed command**.

So check it immediately.

---

## Using exit

A script can return its own status:

```bash
exit 0
```

means success.

Example:

```bash
if [ -f /etc/hosts ]; then
    echo "File exists"
    exit 0
else
    echo "File missing"
    exit 1
fi
```

---

## Using Exit Codes in Conditions

You often do not need `$?` explicitly.

Example:

```bash
if ping -c 1 server01; then
    echo "Server reachable"
else
    echo "Server unreachable"
fi
```

Bash evaluates the command's exit status directly.

---

## Practical Examples

### Service Check

```bash
if systemctl is-active --quiet nginx; then
    echo "nginx is running"
    exit 0
else
    echo "nginx is down"
    exit 1
fi
```

### AND Operator

Run second command only if first succeeds:

```bash
mkdir backup && echo "Directory created"
```

### OR Operator

Run second command only if first fails:

```bash
ping -c 1 server01 || echo "Host unreachable"
```

---

## Common Pitfalls

### Checking `$?` Too Late

Wrong:

```bash
ping -c 1 server01
echo "Checking result..."
echo "$?"
```

Now `$?` refers to `echo`, not `ping`.

### Assuming Every Non-Zero Value Means the Same Thing

Different commands use different exit codes.

Check documentation:

```bash
man COMMAND
```

---

## Related Topics

- `conditionals.md`
- `functions.md`
- `arguments.md`
- `debugging.md`

---

## Conclusion

Exit codes are how Linux commands communicate success or failure.

The key rule is:

```text
0 = success
non-zero = failure
```

Reliable scripts use exit codes to decide what should happen next.