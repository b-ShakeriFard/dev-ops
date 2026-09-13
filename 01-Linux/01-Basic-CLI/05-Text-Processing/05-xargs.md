# 📦 xargs

> Turning standard input into command-line arguments for another command.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Using with find](#using-with-find)
- [Using Placeholders](#using-placeholders)
- [Safe Filename Handling](#safe-filename-handling)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `command | xargs OTHER` | Pass input as arguments |
| `xargs -n 1` | One argument per command |
| `xargs -I {}` | Use placeholder |
| `xargs -p` | Ask before execution |
| `xargs -0` | Read null-separated input |
| `find ... -print0 \| xargs -0 ...` | Safely handle filenames |

---

## Overview

Many Linux commands produce output like:

```text
file1
file2
file3
```

`xargs` takes that input and builds command arguments.

Example:

```bash
printf "file1 file2 file3\n" | xargs touch
```

Equivalent idea:

```bash
touch file1 file2 file3
```

Mental model:

```text
stdin
  ↓
xargs
  ↓
command arguments
```

---

## Basic Usage

Example:

```bash
echo "one two three" | xargs echo
```

Run one argument at a time:

```bash
echo "one two three" | xargs -n 1 echo
```

Output:

```text
one
two
three
```

---

## Using with find

A common pattern:

```bash
find /tmp -name "*.log" | xargs rm
```

But this can break with filenames containing spaces.

A safer form is:

```bash
find /tmp -name "*.log" -print0 | xargs -0 rm
```

---

## Using Placeholders

Use:

```bash
-I {}
```

Example:

```bash
printf "web01\nweb02\n" |
xargs -I {} echo "Checking {}"
```

Output:

```text
Checking web01
Checking web02
```

Another example:

```bash
cat users.txt | xargs -I {} id {}
```

---

## Safe Filename Handling

For filenames, prefer:

```bash
find . -type f -print0 | xargs -0 COMMAND
```

Why?

Because filenames may contain:

```text
Spaces
Tabs
Newlines
Special characters
```

Null-separated input avoids ambiguity.

---

## Practical Examples

Remove old temporary files:

```bash
find /tmp -name "*.tmp" -print0 |
xargs -0 rm
```

Check several hosts:

```bash
printf "server01\nserver02\n" |
xargs -n 1 ping -c 1
```

Search several files:

```bash
find . -name "*.conf" -print0 |
xargs -0 grep -i "port"
```

Ask before each generated command:

```bash
echo "file1 file2" | xargs -p rm
```

---

## Common Pitfalls

### Filenames with Spaces

Unsafe:

```bash
find . -type f | xargs rm
```

Safer:

```bash
find . -type f -print0 | xargs -0 rm
```

### Empty Input

Some implementations may still invoke the command.

With GNU `xargs`, use:

```bash
xargs -r
```

to avoid running when there is no input.

### Unnecessary xargs

Sometimes the command already supports input directly.

Example:

```bash
grep pattern file
```

does not need `xargs`.

---

## Related Topics

- `awk.md`
- `sed.md`
- `cut.md`
- `sort-uniq.md`
- `../04-Searching/find.md`

---

## Conclusion

`xargs` connects:

```text
Command output
      ↓
Another command's arguments
```

The safest and most important pattern is:

```bash
find ... -print0 | xargs -0 COMMAND
```

It is extremely useful when building Linux command pipelines.