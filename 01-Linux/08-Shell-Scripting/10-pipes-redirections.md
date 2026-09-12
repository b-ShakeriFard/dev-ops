# 🔀 Bash Pipes & Redirection

> Controlling where command input and output come from and where they go.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Standard Streams](#standard-streams)
- [Output Redirection](#output-redirection)
- [Error Redirection](#error-redirection)
- [Pipes](#pipes)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `>` | Redirect output, overwrite file |
| `>>` | Redirect output, append |
| `<` | Read input from file |
| `2>` | Redirect errors |
| `2>>` | Append errors |
| `2>&1` | Send stderr to stdout |
| `|` | Pipe output to another command |
| `/dev/null` | Discard output |

---

## Overview

Linux commands normally use three standard streams:

```text
stdin   → input
stdout  → normal output
stderr  → error output
```

Their file descriptor numbers are:

```text
stdin  = 0
stdout = 1
stderr = 2
```

---

## Output Redirection

Overwrite a file:

```bash
echo "hello" > file.txt
```

Append instead:

```bash
echo "world" >> file.txt
```

Redirect command output:

```bash
df -h > disk-report.txt
```

Read input from a file:

```bash
sort < names.txt
```

---

## Error Redirection

Redirect only errors:

```bash
command 2> errors.log
```

Redirect normal and error output together:

```bash
command > output.log 2>&1
```

Modern Bash also supports:

```bash
command &> output.log
```

Discard errors:

```bash
command 2> /dev/null
```

---

## Pipes

A pipe sends one command's standard output into another command's standard input.

Example:

```bash
ps aux | grep nginx
```

Another example:

```bash
journalctl | grep error
```

Count matching lines:

```bash
grep "Failed" app.log | wc -l
```

---

## Practical Examples

### Find Large Files

```bash
du -ah /var/log | sort -h | tail
```

### Save Errors Separately

```bash
./backup.sh > backup.log 2> backup-errors.log
```

### Log Everything

```bash
./script.sh > script.log 2>&1
```

### Ignore Output

```bash
ping -c 1 server01 > /dev/null 2>&1
```

Useful when only the exit code matters.

---

## Common Pitfalls

### Accidentally Overwriting Files

This:

```bash
command > file.txt
```

replaces existing content.

Use:

```bash
>>
```

when you want to append.

### Redirection Order Matters

These are not always equivalent:

```bash
command > file 2>&1
command 2>&1 > file
```

The first sends both stdout and stderr to the file.

---

## Related Topics

- `input-output.md`
- `command-substitution.md`
- `exit-codes.md`
- `practical-scripts.md`

---

## Conclusion

Pipes and redirection let commands work together.

The core mental model is:

```text
Command
  ↓
stdout
  ↓
Pipe / File / Another Command
```

They are essential for building useful Linux automation workflows.