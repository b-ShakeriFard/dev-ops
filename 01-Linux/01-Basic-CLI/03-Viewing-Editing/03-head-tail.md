# 🔍 head & tail

> Viewing the beginning or end of text files quickly.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using head](#using-head)
- [Using tail](#using-tail)
- [Following Logs](#following-logs)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `head FILE` | Show first 10 lines |
| `head -n 20 FILE` | Show first 20 lines |
| `tail FILE` | Show last 10 lines |
| `tail -n 20 FILE` | Show last 20 lines |
| `tail -f FILE` | Follow new lines live |
| `tail -F FILE` | Follow even after log rotation |

---

## Overview

`head` and `tail` are useful when you do not need to read an entire file.

Use:

```text
head → beginning of file
tail → end of file
```

They are especially useful for:

```text
Logs
CSV files
Configuration files
Command output
```

---

## Using head

Show the first 10 lines:

```bash
head file.txt
```

Show the first 20 lines:

```bash
head -n 20 file.txt
```

Short form:

```bash
head -20 file.txt
```

View the first lines of command output:

```bash
ps aux | head
```

---

## Using tail

Show the last 10 lines:

```bash
tail file.txt
```

Show the last 50 lines:

```bash
tail -n 50 file.txt
```

Example:

```bash
tail -n 50 /var/log/messages
```

---

## Following Logs

Follow a file as new lines are written:

```bash
tail -f application.log
```

This is extremely useful during troubleshooting.

Example:

```bash
sudo tail -f /var/log/nginx/error.log
```

Stop with:

```text
Ctrl+C
```

A more resilient option is:

```bash
tail -F application.log
```

`-F` keeps following the file even if it is renamed or recreated during log rotation.

---

## Practical Examples

Show the first row of a CSV:

```bash
head -n 1 data.csv
```

Inspect recent authentication logs:

```bash
tail -n 30 /var/log/secure
```

Watch logs while restarting a service:

```bash
tail -f /var/log/myapp/app.log
```

Show everything except the first line:

```bash
tail -n +2 data.csv
```

Useful for skipping CSV headers.

---

## Common Pitfalls

### Assuming 10 Lines Is Always Enough

The default is only:

```text
10 lines
```

Use `-n` when you need more.

### Using `tail -f` on Rotated Logs

After log rotation, `tail -f` may continue tracking the old file.

Prefer:

```bash
tail -F FILE
```

when rotation is expected.

### Huge Command Output

Instead of:

```bash
command
```

you can preview:

```bash
command | head
```

---

## Related Topics

- `cat.md`
- `less.md`
- `../../../09-Logs-Monitoring/log-files.md`
- `../../../09-Logs-Monitoring/logrotate.md`

---

## Conclusion

The core patterns are:

```bash
head FILE
tail FILE
tail -f FILE
```

For day-to-day administration, `tail -f` is particularly valuable for watching logs in real time.