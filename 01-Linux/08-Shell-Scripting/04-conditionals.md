# 🔀 Bash Conditionals

> Making decisions inside shell scripts with `if`, tests, and comparisons.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [if Statements](#if-statements)
- [File Tests](#file-tests)
- [String and Number Tests](#string-and-number-tests)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `if ...; then` | Start condition |
| `elif` | Additional condition |
| `else` | Fallback action |
| `fi` | End condition |
| `-f FILE` | Regular file exists |
| `-d DIR` | Directory exists |
| `-z "$VAR"` | String is empty |
| `-eq` | Numbers equal |
| `==` | Strings equal |

---

## Overview

Conditionals let scripts make decisions.

Basic structure:

```bash
if CONDITION; then
    COMMAND
fi
```

Example:

```bash
if systemctl is-active --quiet nginx; then
    echo "nginx is running"
fi
```

---

## if Statements

Full example:

```bash
if CONDITION; then
    echo "Condition is true"
elif OTHER_CONDITION; then
    echo "Second condition"
else
    echo "No condition matched"
fi
```

---

## File Tests

Check whether a file exists:

```bash
if [ -f /etc/hosts ]; then
    echo "File exists"
fi
```

Useful tests:

```text
-f → regular file
-d → directory
-e → path exists
-r → readable
-w → writable
-x → executable
```

---

## String and Number Tests

String:

```bash
if [ "$USER" = "root" ]; then
    echo "Running as root"
fi
```

Empty string:

```bash
if [ -z "$NAME" ]; then
    echo "Name is empty"
fi
```

Numbers:

```bash
if [ "$COUNT" -gt 10 ]; then
    echo "Greater than 10"
fi
```

Common numeric operators:

```text
-eq → equal
-ne → not equal
-gt → greater than
-lt → less than
-ge → greater/equal
-le → less/equal
```

---

## Practical Examples

### Check Disk Usage

```bash
USAGE=85

if [ "$USAGE" -ge 80 ]; then
    echo "Disk usage warning"
else
    echo "Disk usage OK"
fi
```

### Check Service

```bash
if systemctl is-active --quiet sshd; then
    echo "SSH is running"
else
    echo "SSH is stopped"
fi
```

---

## Common Pitfalls

### Missing Spaces

Wrong:

```bash
if ["$USER" = "root"]; then
```

Correct:

```bash
if [ "$USER" = "root" ]; then
```

### Unquoted Variables

Prefer:

```bash
[ "$NAME" = "alice" ]
```

rather than:

```bash
[ $NAME = alice ]
```

---

## Related Topics

- `variables.md`
- `loops.md`
- `exit-codes.md`
- `practical-scripts.md`

---

## Conclusion

Conditionals give scripts decision-making logic.

The core pattern is:

```text
Test condition
     ↓
Run matching action
     ↓
Continue script
```

`if`, `elif`, and `else` are fundamental building blocks of reliable automation.