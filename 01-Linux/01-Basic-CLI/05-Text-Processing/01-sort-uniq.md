# 🔤 sort & uniq

> Sorting text and identifying duplicate lines from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using sort](#using-sort)
- [Using uniq](#using-uniq)
- [Using Them Together](#using-them-together)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `sort FILE` | Sort lines alphabetically |
| `sort -r FILE` | Reverse sort |
| `sort -n FILE` | Numeric sort |
| `sort -k2 FILE` | Sort by field 2 |
| `uniq FILE` | Remove adjacent duplicates |
| `uniq -c FILE` | Count duplicates |
| `uniq -d FILE` | Show repeated lines |
| `sort FILE \| uniq` | Sort and remove duplicates |

---

## Overview

`sort` orders lines.

`uniq` detects repeated **adjacent** lines.

Example:

```text
banana
apple
banana
orange
```

Run:

```bash
sort fruits.txt
```

Output:

```text
apple
banana
banana
orange
```

Now:

```bash
sort fruits.txt | uniq
```

Output:

```text
apple
banana
orange
```

---

## Using sort

Alphabetical sort:

```bash
sort names.txt
```

Reverse order:

```bash
sort -r names.txt
```

Numeric sort:

```bash
sort -n numbers.txt
```

Example:

```text
2
10
100
```

Without `-n`, sorting is textual rather than numeric.

Sort by a field:

```bash
sort -k2 users.txt
```

---

## Using uniq

Remove adjacent duplicate lines:

```bash
uniq names.txt
```

Count occurrences:

```bash
uniq -c names.txt
```

Show only duplicated lines:

```bash
uniq -d names.txt
```

Show only unique lines:

```bash
uniq -u names.txt
```

---

## Using Them Together

Because `uniq` only works on adjacent duplicates, it is commonly paired with `sort`.

Example:

```bash
sort users.txt | uniq
```

Count each value:

```bash
sort users.txt | uniq -c
```

Sort counts numerically:

```bash
sort users.txt | uniq -c | sort -nr
```

---

## Practical Examples

Count repeated IP addresses:

```bash
awk '{print $1}' access.log | sort | uniq -c
```

Find the most common values:

```bash
awk '{print $1}' access.log \
| sort \
| uniq -c \
| sort -nr
```

Remove duplicate usernames:

```bash
cut -d: -f1 users.txt | sort | uniq
```

---

## Common Pitfalls

### Using uniq Without Sorting

This:

```bash
uniq file.txt
```

only removes duplicates that are already next to each other.

Usually use:

```bash
sort file.txt | uniq
```

### Numeric Data Sorted Alphabetically

Wrong:

```bash
sort numbers.txt
```

For numbers:

```bash
sort -n numbers.txt
```

---

## Related Topics

- `cut.md`
- `awk.md`
- `sed.md`
- `xargs.md`

---

## Conclusion

The essential pattern is:

```bash
sort FILE | uniq
```

For counting repeated values:

```bash
sort FILE | uniq -c
```

Together, `sort` and `uniq` are extremely useful for logs, command output, and quick data analysis.