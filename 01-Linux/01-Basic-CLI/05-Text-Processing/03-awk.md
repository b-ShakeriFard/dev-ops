# 🧮 awk

> Extracting, filtering, and processing structured text from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Fields and Records](#fields-and-records)
- [Filtering](#filtering)
- [Built-in Variables](#built-in-variables)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `awk '{print $1}' FILE` | Print first field |
| `awk '{print $1,$3}' FILE` | Print selected fields |
| `awk -F: '{print $1}' FILE` | Use `:` as delimiter |
| `awk '$3 > 1000' FILE` | Filter by condition |
| `awk 'NR==1' FILE` | Show first line |
| `awk '{print NF}' FILE` | Show field count |

---

## Overview

`awk` is designed for processing text line by line.

It treats input as:

```text
Records → lines
Fields  → columns
```

Example:

```text
alice developer 1200
bob admin 950
carol developer 1400
```

Command:

```bash
awk '{print $1}' users.txt
```

Output:

```text
alice
bob
carol
```

---

## Fields and Records

By default, `awk` splits fields on whitespace.

Special field variables:

```text
$0 → entire line
$1 → first field
$2 → second field
$3 → third field
```

Example:

```bash
awk '{print $1, $3}' users.txt
```

---

## Custom Delimiters

For `/etc/passwd`, use `:`:

```bash
awk -F: '{print $1}' /etc/passwd
```

Print username and shell:

```bash
awk -F: '{print $1, $7}' /etc/passwd
```

---

## Filtering

Show lines where field 3 is greater than 1000:

```bash
awk '$3 > 1000' users.txt
```

Combine filtering and printing:

```bash
awk '$3 > 1000 {print $1}' users.txt
```

Match text:

```bash
awk '/error/ {print}' app.log
```

---

## Built-in Variables

Useful variables:

```text
NR → current line number
NF → number of fields
FS → input field separator
```

Print line numbers:

```bash
awk '{print NR, $0}' file.txt
```

Print last field:

```bash
awk '{print $NF}' file.txt
```

---

## Practical Examples

Show usernames:

```bash
awk -F: '{print $1}' /etc/passwd
```

Show users with UID >= 1000:

```bash
awk -F: '$3 >= 1000 {print $1}' /etc/passwd
```

Sum values:

```bash
awk '{sum += $2} END {print sum}' data.txt
```

Check disk usage:

```bash
df -h | awk 'NR>1 {print $1, $5}'
```

---

## Common Pitfalls

### Confusing Shell Variables and awk Fields

Inside awk:

```text
$1
```

means field 1, not a Bash positional argument.

### Complex Quoting

Awk programs are usually wrapped in single quotes:

```bash
awk '{print $1}' file
```

### Overusing awk

For simple fixed delimiters, `cut` may be clearer.

Use `awk` when you need:

```text
Filtering
Calculations
Conditions
Flexible whitespace
```

---

## Related Topics

- `cut.md`
- `sort-uniq.md`
- `sed.md`
- `xargs.md`

---

## Conclusion

The core mental model is:

```text
Read line
   ↓
Split into fields
   ↓
Test condition
   ↓
Print or calculate
```

For Linux administration, `awk` is one of the most powerful tools for turning command output into useful information.