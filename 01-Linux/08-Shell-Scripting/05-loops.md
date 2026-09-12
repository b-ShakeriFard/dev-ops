# 🔁 Bash Loops

> Repeating commands efficiently with `for`, `while`, and `until`.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [for Loops](#for-loops)
- [while Loops](#while-loops)
- [until Loops](#until-loops)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `for ...; do ... done` | Loop over values |
| `while CONDITION; do ... done` | Repeat while true |
| `until CONDITION; do ... done` | Repeat until true |
| `break` | Exit loop |
| `continue` | Skip current iteration |
| `seq 1 5` | Generate number sequence |

---

## Overview

Loops let scripts repeat tasks automatically.

Typical uses:

```text
Check several servers
Process many files
Create multiple users
Retry a command
```

---

## for Loops

Loop over values:

```bash
for SERVER in web01 web02 web03; do
    echo "Checking $SERVER"
done
```

Loop over files:

```bash
for FILE in *.log; do
    echo "$FILE"
done
```

Number sequence:

```bash
for N in $(seq 1 5); do
    echo "$N"
done
```

---

## while Loops

Repeat while a condition is true:

```bash
COUNT=1

while [ "$COUNT" -le 5 ]; do
    echo "$COUNT"
    COUNT=$((COUNT + 1))
done
```

Useful when the number of iterations is not fixed beforehand.

---

## until Loops

`until` repeats while a condition is false.

Example:

```bash
until ping -c 1 server01 >/dev/null 2>&1; do
    echo "Waiting for server..."
    sleep 5
done

echo "Server is reachable"
```

---

## Practical Examples

### Check Several Servers

```bash
for SERVER in server01 server02 server03; do
    ping -c 1 "$SERVER"
done
```

### Process Log Files

```bash
for FILE in /var/log/*.log; do
    echo "Checking $FILE"
done
```

### Stop a Loop

```bash
for N in 1 2 3 4 5; do
    if [ "$N" -eq 3 ]; then
        break
    fi
done
```

---

## Common Pitfalls

### Unquoted Variables

Prefer:

```bash
"$FILE"
```

especially when filenames may contain spaces.

### Infinite Loops

Example:

```bash
while true; do
    ...
done
```

Always make sure there is a valid exit condition when appropriate.

---

## Related Topics

- `conditionals.md`
- `functions.md`
- `arguments.md`
- `practical-scripts.md`

---

## Conclusion

Use:

```text
for   → iterate over known values
while → repeat while condition is true
until → repeat until condition becomes true
```

Loops are one of the main tools for turning repetitive administration into automation.