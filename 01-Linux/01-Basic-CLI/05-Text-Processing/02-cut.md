# ✂️ cut

> Extracting selected characters, fields, or columns from text.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Character Selection](#character-selection)
- [Field Selection](#field-selection)
- [Delimiters](#delimiters)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `cut -c1 FILE` | First character |
| `cut -c1-5 FILE` | Characters 1 through 5 |
| `cut -d: -f1 FILE` | First colon-separated field |
| `cut -d, -f1,3 FILE` | Fields 1 and 3 |
| `cut -d: -f2- FILE` | Field 2 onward |

---

## Overview

`cut` extracts specific parts of each input line.

It works especially well with structured text such as:

```text
username:uid:shell
alice:1001:/bin/bash
bob:1002:/bin/zsh
```

Example:

```bash
cut -d: -f1 users.txt
```

Output:

```text
username
alice
bob
```

---

## Character Selection

Extract the first character:

```bash
cut -c1 file.txt
```

Characters 1 through 5:

```bash
cut -c1-5 file.txt
```

From character 5 onward:

```bash
cut -c5- file.txt
```

Multiple positions:

```bash
cut -c1,3,5 file.txt
```

---

## Field Selection

Use:

```bash
-f
```

to select fields.

Example:

```bash
cut -f1 file.txt
```

By default, `cut` expects tab-separated input.

For other separators, specify a delimiter.

---

## Delimiters

Colon-separated file:

```bash
cut -d: -f1 /etc/passwd
```

This prints usernames.

CSV example:

```bash
cut -d, -f2 data.csv
```

Multiple fields:

```bash
cut -d: -f1,7 /etc/passwd
```

This displays:

```text
username:shell
```

---

## Practical Examples

Show usernames:

```bash
cut -d: -f1 /etc/passwd
```

Show usernames and UIDs:

```bash
cut -d: -f1,3 /etc/passwd
```

Extract domains from a simple email list:

```bash
cut -d@ -f2 emails.txt
```

Combine with `sort`:

```bash
cut -d: -f7 /etc/passwd \
| sort \
| uniq -c
```

This counts how many users use each shell.

---

## Common Pitfalls

### Assuming Whitespace Is a Delimiter

This can be unreliable:

```bash
cut -d' ' -f2 file.txt
```

because multiple spaces create empty fields.

For variable whitespace, `awk` is usually better.

### Expecting CSV Awareness

`cut` does not understand quoted CSV fields.

For complex CSV data, use a proper CSV-aware tool.

### Forgetting the Delimiter

If the input uses `:`, specify:

```bash
-d:
```

---

## Related Topics

- `sort-uniq.md`
- `awk.md`
- `sed.md`
- `xargs.md`

---

## Conclusion

The essential pattern is:

```bash
cut -dDELIMITER -fFIELD FILE
```

Use `cut` when the text has a simple and predictable structure.