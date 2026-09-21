# 🔍 grep

> Searching for text patterns inside files and command output.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Searching](#basic-searching)
- [Useful Options](#useful-options)
- [Recursive Searching](#recursive-searching)
- [Regular Expressions](#regular-expressions)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `grep TEXT FILE` | Search for text |
| `grep -i TEXT FILE` | Ignore case |
| `grep -n TEXT FILE` | Show line numbers |
| `grep -v TEXT FILE` | Show non-matching lines |
| `grep -c TEXT FILE` | Count matching lines |
| `grep -R TEXT DIR` | Search recursively |
| `grep -w WORD FILE` | Match whole word |
| `grep -E 'A|B' FILE` | Extended regex |

---

## Overview

`grep` searches text for matching patterns.

Example:

```bash
grep "error" application.log
```

This prints lines containing:

```text
error
```

`grep` is commonly used with:

```text
Logs
Configuration files
Command output
Scripts
System files
```

---

## Basic Searching

Search one file:

```bash
grep "root" /etc/passwd
```

Search multiple files:

```bash
grep "ERROR" *.log
```

Ignore case:

```bash
grep -i "error" app.log
```

Show line numbers:

```bash
grep -n "PermitRootLogin" /etc/ssh/sshd_config
```

---

## Useful Options

Invert the match:

```bash
grep -v "DEBUG" app.log
```

Count matching lines:

```bash
grep -c "ERROR" app.log
```

Whole-word match:

```bash
grep -w "root" file.txt
```

Show surrounding lines:

```bash
grep -C 2 "ERROR" app.log
```

Or:

```bash
grep -A 3 "ERROR" app.log
grep -B 3 "ERROR" app.log
```

Where:

```text
-A → after
-B → before
-C → context
```

---

## Recursive Searching

Search a directory tree:

```bash
grep -R "listen" /etc/nginx/
```

Include line numbers:

```bash
grep -Rn "listen" /etc/nginx/
```

Search only `.conf` files:

```bash
grep -R \
  --include="*.conf" \
  "listen" /etc/nginx/
```

---

## Regular Expressions

Beginning of line:

```bash
grep '^root' /etc/passwd
```

End of line:

```bash
grep 'bash$' /etc/passwd
```

Extended regex:

```bash
grep -E 'error|warning|failed' app.log
```

Ignore case too:

```bash
grep -Ei 'error|warning|failed' app.log
```

---

## Practical Examples

Find failed SSH logins:

```bash
grep -i "failed" /var/log/secure
```

Search processes:

```bash
ps aux | grep nginx
```

Avoid matching the `grep` command itself:

```bash
ps aux | grep '[n]ginx'
```

Search configuration:

```bash
grep -Rn "server_name" /etc/nginx/
```

Count errors:

```bash
grep -ci "error" application.log
```

---

## Common Pitfalls

### Case Sensitivity

This:

```bash
grep "error" file
```

does not match:

```text
ERROR
Error
```

Use:

```bash
grep -i
```

### Binary Files

`grep` may report:

```text
Binary file matches
```

For text-oriented searches, restrict the file types where possible.

### Complex Patterns

Remember that `grep` patterns are regular expressions by default.

For a literal string:

```bash
grep -F '192.168.1.1' file.txt
```

### Recursive Search Can Be Huge

Instead of:

```bash
grep -R "text" /
```

narrow the search path whenever possible.

---

## Related Topics

- `find.md`
- `locate.md`
- `../05-Text-Processing/sed.md`
- `../../../09-Logs-Monitoring/log-files.md`

---

## Conclusion

The most useful forms are:

```bash
grep -i TEXT FILE
grep -n TEXT FILE
grep -R TEXT DIR
grep -E 'A|B' FILE
```

For Linux administration, `grep` is one of the fastest ways to turn large amounts of text into useful information.