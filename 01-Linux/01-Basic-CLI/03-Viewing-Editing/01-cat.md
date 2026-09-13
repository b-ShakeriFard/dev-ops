# 🐱 cat

> Displaying, combining, and inspecting text files from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Combining Files](#combining-files)
- [Useful Options](#useful-options)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `cat FILE` | Display file contents |
| `cat FILE1 FILE2` | Display multiple files |
| `cat FILE1 FILE2 > FILE3` | Combine files |
| `cat -n FILE` | Number all lines |
| `cat -b FILE` | Number non-empty lines |
| `cat -A FILE` | Show hidden characters |

---

## Overview

`cat` originally means:

```text
concatenate
```

It is commonly used to print the contents of short text files.

Example:

```bash
cat notes.txt
```

For large files, `less` is usually more convenient.

---

## Basic Usage

Display one file:

```bash
cat file.txt
```

Display several files:

```bash
cat file1.txt file2.txt
```

The contents are printed one after another.

---

## Combining Files

Combine two files into a new file:

```bash
cat file1.txt file2.txt > combined.txt
```

Append instead:

```bash
cat file2.txt >> combined.txt
```

Be careful:

```bash
>
```

overwrites the destination file.

---

## Useful Options

Number all lines:

```bash
cat -n file.txt
```

Number only non-empty lines:

```bash
cat -b file.txt
```

Show invisible characters:

```bash
cat -A file.txt
```

This can help detect:

```text
Tabs
Trailing spaces
Control characters
Line-ending problems
```

---

## Practical Examples

View a configuration file:

```bash
cat /etc/hosts
```

Display OS information:

```bash
cat /etc/os-release
```

Combine configuration fragments:

```bash
cat header.conf body.conf > full.conf
```

Create a small file interactively:

```bash
cat > notes.txt
```

Type content, then press:

```text
Ctrl+D
```

to finish.

---

## Common Pitfalls

### Using cat on Huge Files

This:

```bash
cat huge.log
```

may flood the terminal.

Prefer:

```bash
less huge.log
```

### Accidentally Overwriting a File

Be careful with:

```bash
cat file1 > file2
```

If `file2` already exists, its previous content is replaced.

### Binary Files

`cat` is intended mainly for text.

Printing binary files may produce unreadable terminal output.

---

## Related Topics

- `less.md`
- `head-tail.md`
- `../05-Text-Processing/`
- `../../../09-Logs-Monitoring/log-files.md`

---

## Conclusion

Use `cat` when you want to quickly inspect or combine small text files.

The most common patterns are:

```bash
cat FILE
cat FILE1 FILE2
cat FILE1 FILE2 > OUTPUT
```