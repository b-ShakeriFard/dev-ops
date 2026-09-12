# 🎯 Bash Script Arguments

> Passing values into shell scripts from the command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Positional Arguments](#positional-arguments)
- [Special Argument Variables](#special-argument-variables)
- [Practical Examples](#practical-examples)
- [Validating Arguments](#validating-arguments)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Variable | Meaning |
|---|---|
| `$0` | Script name |
| `$1` | First argument |
| `$2` | Second argument |
| `$#` | Number of arguments |
| `$@` | All arguments |
| `$*` | All arguments as one expansion |
| `shift` | Move arguments left |

---

## Overview

Arguments let a script receive values when it starts.

Example:

```bash
./check-host.sh server01
```

Inside the script:

```text
$1 = server01
```

This makes scripts reusable instead of hard-coding values.

---

## Positional Arguments

Example:

```bash
#!/bin/bash

echo "Host: $1"
echo "Port: $2"
```

Run:

```bash
./check-host.sh server01 443
```

Output:

```text
Host: server01
Port: 443
```

---

## Special Argument Variables

Script name:

```bash
echo "$0"
```

Number of arguments:

```bash
echo "$#"
```

All arguments:

```bash
echo "$@"
```

A common loop:

```bash
for ARG in "$@"; do
    echo "$ARG"
done
```

Using `"$@"` preserves each argument separately.

---

## Practical Examples

### Check a File

```bash
#!/bin/bash

FILE="$1"

if [ -f "$FILE" ]; then
    echo "$FILE exists"
else
    echo "$FILE not found"
fi
```

Run:

```bash
./check-file.sh /etc/hosts
```

---

### Host and Port Check

```bash
#!/bin/bash

HOST="$1"
PORT="$2"

nc -zv "$HOST" "$PORT"
```

Run:

```bash
./check-port.sh server01 443
```

---

## Validating Arguments

Always check required arguments.

Example:

```bash
if [ "$#" -ne 2 ]; then
    echo "Usage: $0 HOST PORT"
    exit 1
fi
```

Then:

```bash
HOST="$1"
PORT="$2"
```

---

## Using shift

`shift` removes `$1` and moves the remaining arguments left.

Example:

```bash
echo "$1"

shift

echo "$1"
```

If arguments were:

```text
one two three
```

after `shift`:

```text
$1 = two
```

---

## Common Pitfall

Always quote arguments:

```bash
"$1"
"$@"
```

instead of:

```bash
$1
$@
```

This avoids problems with spaces and special characters.

---

## Related Topics

- `variables.md`
- `functions.md`
- `exit-codes.md`
- `practical-scripts.md`

---

## Conclusion

Arguments make scripts reusable.

The most important variables are:

```text
$1  → first argument
$#  → argument count
$@  → all arguments
```

Good scripts validate their arguments before doing work.