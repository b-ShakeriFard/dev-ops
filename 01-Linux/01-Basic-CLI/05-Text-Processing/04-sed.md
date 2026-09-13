# 🛠️ sed

> Searching, replacing, deleting, and transforming text from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Substitution](#substitution)
- [Deleting Lines](#deleting-lines)
- [Printing Selected Lines](#printing-selected-lines)
- [In-Place Editing](#in-place-editing)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `sed 's/old/new/' FILE` | Replace first match per line |
| `sed 's/old/new/g' FILE` | Replace all matches |
| `sed -n '5p' FILE` | Print line 5 |
| `sed '5d' FILE` | Delete line 5 |
| `sed '/text/d' FILE` | Delete matching lines |
| `sed -i 's/old/new/g' FILE` | Edit file in place |
| `sed -i.bak 's/old/new/g' FILE` | Edit with backup |

---

## Overview

`sed` means:

```text
stream editor
```

It processes text line by line and is commonly used for:

```text
Search and replace
Delete lines
Select lines
Modify configuration files
Transform command output
```

Basic example:

```bash
sed 's/http/https/' file.txt
```

By default, `sed` prints the modified result to stdout without changing the original file.

---

## Substitution

Basic syntax:

```bash
sed 's/OLD/NEW/' file.txt
```

Replace:

```bash
sed 's/dev/prod/' config.txt
```

This replaces only the first match on each line.

Replace every match:

```bash
sed 's/dev/prod/g' config.txt
```

Case-insensitive replacement with GNU `sed`:

```bash
sed 's/error/warning/gi' file.txt
```

---

## Deleting Lines

Delete line 5:

```bash
sed '5d' file.txt
```

Delete lines 5 through 10:

```bash
sed '5,10d' file.txt
```

Delete lines containing text:

```bash
sed '/DEBUG/d' app.log
```

Delete blank lines:

```bash
sed '/^$/d' file.txt
```

---

## Printing Selected Lines

Normally, `sed` prints every line.

Use:

```bash
-n
```

to suppress automatic output.

Print line 5:

```bash
sed -n '5p' file.txt
```

Print lines 10 through 20:

```bash
sed -n '10,20p' file.txt
```

Print lines matching a pattern:

```bash
sed -n '/error/p' app.log
```

---

## In-Place Editing

Modify the actual file:

```bash
sed -i 's/dev/prod/g' config.txt
```

Safer version with backup:

```bash
sed -i.bak 's/dev/prod/g' config.txt
```

This creates:

```text
config.txt.bak
```

before editing.

---

## Practical Examples

Change a port:

```bash
sed 's/8080/9090/g' app.conf
```

Comment matching lines:

```bash
sed 's/^PermitRootLogin/#PermitRootLogin/' sshd_config
```

Remove comments:

```bash
sed '/^#/d' config.txt
```

Remove comments and blank lines:

```bash
sed '/^#/d; /^$/d' config.txt
```

Use another delimiter for paths:

```bash
sed 's|/old/path|/new/path|g' file.txt
```

This avoids escaping `/`.

---

## Common Pitfalls

### Forgetting `g`

This:

```bash
sed 's/foo/bar/' file
```

changes only the first match on each line.

For all matches:

```bash
sed 's/foo/bar/g' file
```

### Editing Before Testing

Before:

```bash
sed -i ...
```

test without `-i`:

```bash
sed 's/old/new/g' file
```

### Regex Characters

Characters such as:

```text
.
*
[
]
^
$
```

have special meaning in regular expressions.

---

## Related Topics

- `awk.md`
- `cut.md`
- `sort-uniq.md`
- `xargs.md`

---

## Conclusion

The most important `sed` pattern is:

```bash
sed 's/OLD/NEW/g' FILE
```

Use `sed` when you need quick, repeatable text transformations without opening an editor.