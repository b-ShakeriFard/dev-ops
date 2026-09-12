# 📚 Bash Arrays

> Storing multiple values inside a single Bash variable.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Arrays](#creating-arrays)
- [Reading Array Values](#reading-array-values)
- [Looping Through Arrays](#looping-through-arrays)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `ARR=(one two three)` | Create array |
| `${ARR[0]}` | First element |
| `${ARR[@]}` | All elements |
| `${#ARR[@]}` | Number of elements |
| `ARR+=(four)` | Add element |
| `unset 'ARR[1]'` | Remove element |

---

## Overview

Arrays let one variable hold multiple values.

Example:

```bash
SERVERS=("web01" "web02" "db01")
```

Instead of creating:

```bash
SERVER1="web01"
SERVER2="web02"
SERVER3="db01"
```

you can keep related values together.

---

## Creating Arrays

Basic array:

```bash
SERVERS=("web01" "web02" "web03")
```

Add another value:

```bash
SERVERS+=("db01")
```

---

## Reading Array Values

First element:

```bash
echo "${SERVERS[0]}"
```

Second element:

```bash
echo "${SERVERS[1]}"
```

All elements:

```bash
echo "${SERVERS[@]}"
```

Count elements:

```bash
echo "${#SERVERS[@]}"
```

Important:

```text
Bash array indexes start at 0
```

---

## Looping Through Arrays

Example:

```bash
for SERVER in "${SERVERS[@]}"; do
    echo "Checking $SERVER"
done
```

Using `"${ARRAY[@]}"` preserves each element separately.

---

## Practical Examples

### Check Several Servers

```bash
SERVERS=("web01" "web02" "db01")

for SERVER in "${SERVERS[@]}"; do
    ping -c 1 "$SERVER"
done
```

### List Services

```bash
SERVICES=("nginx" "sshd" "cron")

for SERVICE in "${SERVICES[@]}"; do
    systemctl status "$SERVICE"
done
```

### Access by Index

```bash
DISKS=("/dev/sda" "/dev/sdb")

echo "Primary disk: ${DISKS[0]}"
```

---

## Removing Elements

Remove one element:

```bash
unset 'SERVERS[1]'
```

Remove entire array:

```bash
unset SERVERS
```

---

## Common Pitfalls

### Forgetting Braces

Prefer:

```bash
"${SERVERS[0]}"
```

not:

```bash
"$SERVERS[0]"
```

### Not Quoting `@`

Prefer:

```bash
"${SERVERS[@]}"
```

This safely preserves elements containing spaces.

### Expecting Continuous Indexes

After:

```bash
unset 'SERVERS[1]'
```

the remaining indexes may not automatically shift.

---

## Related Topics

- `loops.md`
- `variables.md`
- `arguments.md`
- `practical-scripts.md`

---

## Conclusion

Arrays are useful when a script needs to work with multiple related values.

The most important pattern is:

```bash
ARRAY=("one" "two" "three")

for ITEM in "${ARRAY[@]}"; do
    echo "$ITEM"
done
```

This is especially useful for servers, files, services, and configuration values.