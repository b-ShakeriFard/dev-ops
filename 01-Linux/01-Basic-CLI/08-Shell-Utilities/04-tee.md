# 🔀 tee

> Displaying command output while also writing it to one or more files.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Append Mode](#append-mode)
- [Using tee with sudo](#using-tee-with-sudo)
- [Writing to Multiple Files](#writing-to-multiple-files)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `command \| tee FILE` | Show and save output |
| `command \| tee -a FILE` | Append instead of overwrite |
| `command \| tee FILE1 FILE2` | Write to multiple files |
| `echo "text" \| sudo tee FILE` | Write to privileged file |
| `tee FILE < INPUT` | Read stdin and write to file |

---

## Overview

`tee` takes data from standard input and sends it in two directions:

```text
stdin
  ↓
 tee
 ↙  ↘
file  stdout
```

Example:

```bash
echo "hello" | tee output.txt
```

This:

1. displays `hello`
2. writes `hello` to `output.txt`

---

## Basic Usage

Save command output while still seeing it:

```bash
df -h | tee disk-report.txt
```

Without `tee`:

```bash
df -h > disk-report.txt
```

the output goes only to the file.

---

## Append Mode

By default, `tee` overwrites the file.

Append instead:

```bash
echo "new entry" | tee -a log.txt
```

This behaves similarly to:

```bash
>>
```

but still displays the output.

---

## Using tee with sudo

This is especially useful for privileged files.

Example:

```bash
echo "192.168.1.10 web01" |
sudo tee -a /etc/hosts
```

Why not:

```bash
sudo echo "192.168.1.10 web01" >> /etc/hosts
```

Because the shell performs `>>` before `sudo` can give the write operation elevated privileges.

With:

```bash
sudo tee
```

the process writing the file runs with elevated privileges.

---

## Writing to Multiple Files

`tee` can write the same output to several files:

```bash
echo "hello" | tee file1.txt file2.txt
```

Both receive the same content.

---

## Practical Examples

Save command output:

```bash
ip addr | tee network-info.txt
```

Append logs:

```bash
date | tee -a maintenance.log
```

Create a root-owned configuration file:

```bash
printf "APP_ENV=production\n" |
sudo tee /etc/myapp.conf
```

Use inside a pipeline:

```bash
ps aux |
tee processes.txt |
grep nginx
```

The complete process list is saved while `grep` receives the same stream.

---

## Common Pitfalls

### Accidental Overwrite

This:

```bash
command | tee file.txt
```

replaces existing contents.

Use:

```bash
tee -a
```

to append.

### Duplicate Terminal Output with sudo

This:

```bash
echo "text" | sudo tee file
```

also prints `text`.

Suppress terminal output if needed:

```bash
echo "text" | sudo tee file > /dev/null
```

### tee Does Not Elevate the Input Command

Only the `tee` process receives `sudo` here:

```bash
command | sudo tee file
```

If `command` itself requires privileges, it must also be run appropriately.

---

## Related Topics

- `echo-printf.md`
- `../../../08-Shell-Scripting/pipes-redirection.md`
- `../07-Environment/`

---

## Conclusion

The essential pattern is:

```bash
command | tee FILE
```

And for privileged configuration files:

```bash
command | sudo tee FILE
```

`tee` is especially useful when you need to both **see output and save it at the same time**.