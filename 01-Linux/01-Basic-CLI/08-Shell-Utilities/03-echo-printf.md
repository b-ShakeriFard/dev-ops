# 🗣️ echo & printf

> Printing text, variables, and formatted output from the Linux shell.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using echo](#using-echo)
- [Using printf](#using-printf)
- [Escape Sequences](#escape-sequences)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `echo "text"` | Print text |
| `echo "$VAR"` | Print variable |
| `echo -n "text"` | No trailing newline |
| `printf "%s\n" "$VAR"` | Print formatted string |
| `printf "%d\n" 42` | Print integer |
| `printf "%.2f\n" 3.14159` | Print decimal |

---

## Overview

Both `echo` and `printf` display output.

Example:

```bash
echo "Hello Linux"
```

Output:

```text
Hello Linux
```

For simple messages, `echo` is convenient.

For predictable formatting, `printf` is generally more powerful.

---

## Using echo

Print text:

```bash
echo "System ready"
```

Print variables:

```bash
HOST="server01"

echo "Host: $HOST"
```

No newline:

```bash
echo -n "Loading..."
```

Print several values:

```bash
echo "$USER" "$HOME" "$SHELL"
```

---

## Using printf

Basic syntax:

```bash
printf "FORMAT" VALUES
```

Example:

```bash
printf "User: %s\n" "$USER"
```

Multiple values:

```bash
printf "Host: %s Port: %d\n" "web01" 8080
```

Common format specifiers:

```text
%s → string
%d → integer
%f → floating-point number
\n → newline
\t → tab
```

---

## Escape Sequences

Example:

```bash
printf "Name:\tAlice\nRole:\tAdmin\n"
```

Output:

```text
Name:   Alice
Role:   Admin
```

With `echo`, escape handling can vary between shells.

For portable formatting, prefer:

```bash
printf
```

---

## Practical Examples

Create a simple report:

```bash
printf "Host: %s\n" "$(hostname)"
printf "User: %s\n" "$(whoami)"
printf "Date: %s\n" "$(date +%F)"
```

Formatted table:

```bash
printf "%-10s %-8s\n" "SERVICE" "STATUS"
printf "%-10s %-8s\n" "nginx" "running"
printf "%-10s %-8s\n" "sshd" "running"
```

Write to a file:

```bash
printf "server01\nserver02\n" > servers.txt
```

---

## Common Pitfalls

### Unquoted Variables

Prefer:

```bash
echo "$VAR"
```

rather than:

```bash
echo $VAR
```

### Relying on echo Escape Behavior

This may behave differently across shells:

```bash
echo -e "one\ntwo"
```

Prefer:

```bash
printf "one\ntwo\n"
```

### Format Strings from Variables

Safer:

```bash
printf '%s\n' "$VALUE"
```

rather than using uncontrolled text as the format string.

---

## Related Topics

- `tee.md`
- `history.md`
- `alias-unalias.md`
- `../../../08-Shell-Scripting/input-output.md`

---

## Conclusion

Use:

```bash
echo
```

for quick output.

Use:

```bash
printf
```

when formatting must be precise and predictable.

For scripts, `printf` is often the safer choice.