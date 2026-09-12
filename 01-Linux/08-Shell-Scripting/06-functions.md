# 🧩 Bash Functions

> Grouping reusable commands into named blocks.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Functions](#creating-functions)
- [Function Arguments](#function-arguments)
- [Return Status](#return-status)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `name() { ...; }` | Define function |
| `name` | Call function |
| `$1`, `$2` | Function arguments |
| `$@` | All arguments |
| `return N` | Set function exit status |
| `$?` | Read previous status |

---

## Overview

Functions let you group commands and reuse them.

Instead of repeating:

```bash
echo "Checking server..."
hostname
uptime
```

you can define:

```bash
system_check() {
    echo "Checking server..."
    hostname
    uptime
}
```

Then call:

```bash
system_check
```

---

## Creating Functions

Common syntax:

```bash
check_disk() {
    df -h
}
```

Call:

```bash
check_disk
```

Another valid form:

```bash
function check_disk {
    df -h
}
```

The first style is more portable and commonly preferred.

---

## Function Arguments

Functions receive arguments similarly to scripts.

Example:

```bash
greet() {
    echo "Hello $1"
}

greet "Alice"
```

Multiple arguments:

```bash
check_host() {
    echo "Host: $1"
    echo "Port: $2"
}

check_host server01 443
```

Inside the function:

```text
$1 → first argument
$2 → second argument
$@ → all arguments
$# → number of arguments
```

---

## Return Status

Functions return an exit status.

Example:

```bash
check_file() {
    if [ -f "$1" ]; then
        return 0
    else
        return 1
    fi
}
```

Use it:

```bash
if check_file /etc/hosts; then
    echo "File exists"
fi
```

Convention:

```text
0     → success
non-0 → failure
```

---

## Practical Examples

### Check Service

```bash
check_service() {
    systemctl is-active --quiet "$1"
}
```

Use:

```bash
if check_service sshd; then
    echo "SSH is running"
fi
```

### Reusable Log Message

```bash
log() {
    echo "$(date '+%F %T') - $*"
}

log "Backup started"
```

---

## Common Pitfall

Variables are global by default inside Bash functions.

Use:

```bash
local NAME="value"
```

to keep a variable inside the function.

Example:

```bash
hello() {
    local NAME="$1"
    echo "Hello $NAME"
}
```

---

## Related Topics

- `arguments.md`
- `exit-codes.md`
- `conditionals.md`
- `practical-scripts.md`

---

## Conclusion

Functions make Bash scripts cleaner and reusable.

The basic pattern is:

```bash
name() {
    commands
}
```

For larger scripts, use functions to separate tasks into small, understandable pieces.