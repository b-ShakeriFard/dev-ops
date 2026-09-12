# ⌨️ Bash Input & Output

> Reading user input and displaying information from shell scripts.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Output with echo](#output-with-echo)
- [Output with printf](#output-with-printf)
- [Reading Input](#reading-input)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `echo "text"` | Print simple output |
| `printf "..."` | Print formatted output |
| `read VAR` | Read user input |
| `read -p "Prompt" VAR` | Prompt and read input |
| `read -s VAR` | Read input silently |
| `read -r VAR` | Preserve backslashes |

---

## Overview

Shell scripts often need to:

```text
Display information
        +
Read input
```

Example:

```bash
echo "Enter your name:"
read NAME
echo "Hello $NAME"
```

---

## Output with echo

Basic output:

```bash
echo "Hello Linux"
```

Print a variable:

```bash
NAME="alice"

echo "Hello $NAME"
```

Print multiple values:

```bash
echo "Host:" "$(hostname)"
```

---

## Output with printf

`printf` gives more control over formatting.

Example:

```bash
printf "User: %s\n" "$USER"
```

Multiple values:

```bash
printf "Host: %s | Port: %s\n" "$HOST" "$PORT"
```

Common format symbols:

```text
%s → string
%d → integer
\n → new line
```

---

## Reading Input

Basic:

```bash
read NAME
```

Prompt:

```bash
read -p "Enter username: " NAME
```

Use the value:

```bash
echo "Creating user $NAME"
```

---

## Silent Input

Useful for sensitive input:

```bash
read -s -p "Enter password: " PASSWORD
echo
```

The typed value is not displayed.

Avoid printing or logging sensitive variables afterward.

---

## Practical Examples

### Ask for Server Name

```bash
#!/bin/bash

read -p "Server name: " SERVER

echo "Checking $SERVER..."

ping -c 2 "$SERVER"
```

### Confirmation Prompt

```bash
read -p "Continue? [y/N]: " ANSWER

echo "You entered: $ANSWER"
```

Conditionals can later make this useful.

---

## Common Pitfalls

### Unquoted Variables

Prefer:

```bash
echo "$NAME"
```

rather than:

```bash
echo $NAME
```

### Backslashes Changed by read

For raw input, use:

```bash
read -r VALUE
```

### Using echo for Complex Formatting

For predictable formatting, prefer:

```bash
printf
```

---

## Related Topics

- `variables.md`
- `conditionals.md`
- `arguments.md`
- `pipes-redirection.md`

---

## Conclusion

The core Bash I/O tools are:

```text
echo / printf
→ output

read
→ input
```

For simple scripts, these commands are enough to build basic interactive workflows.