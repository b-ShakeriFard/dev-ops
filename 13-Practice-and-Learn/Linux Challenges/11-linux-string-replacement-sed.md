# Linux String Replacement with `sed`

## Overview

This challenge focused on performing an in-place string replacement inside an XML file on a Linux system.

The task was:

- locate the XML file at `/root/nautilus.xml`
- replace **all occurrences** of the string `Text`
- replace them with `Sonar`
- modify the original file directly

The command used was:

```bash
sudo sed -i 's/Text/Sonar/g' /root/nautilus.xml
```

This is a classic example of using `sed` for automated text manipulation.

---

## What is `sed`?

`sed` stands for **stream editor**.

It is commonly used to:

- search text
- replace text
- delete lines
- insert or append text
- perform automated edits in scripts

Unlike a normal interactive editor such as `vi` or `nano`, `sed` is designed to process text from the command line.

---

## The Command

```bash
sudo sed -i 's/Text/Sonar/g' /root/nautilus.xml
```

### Breakdown

| Part | Meaning |
|---|---|
| `sudo` | Run with elevated privileges |
| `sed` | Stream editor |
| `-i` | Edit the file in place |
| `s` | Substitute |
| `Text` | String to search for |
| `Sonar` | Replacement string |
| `g` | Replace all matches on each line |
| `/root/nautilus.xml` | Target file |

---

## Understanding the Substitution Expression

The important expression is:

```bash
s/Text/Sonar/g
```

Its structure is:

```text
s/OLD/NEW/g
```

So:

```text
s       -> substitution
Text    -> old value
Sonar   -> new value
g       -> global replacement on each line
```

Without the `g` flag:

```bash
sed 's/Text/Sonar/' file
```

only the first matching occurrence on each line would be replaced.

With:

```bash
sed 's/Text/Sonar/g' file
```

all occurrences on each line are replaced.

---

## Why `sudo` Was Required

The file was located under:

```bash
/root/nautilus.xml
```

The `/root` directory belongs to the root user and is normally inaccessible to regular users.

Therefore:

```bash
sed -i ...
```

would fail without sufficient privileges.

Using:

```bash
sudo
```

allows the command to modify the file.

---

## Preview Before Editing

A useful habit is to preview the change before modifying the file.

Run:

```bash
sudo sed 's/Text/Sonar/g' /root/nautilus.xml
```

Notice that there is **no `-i`**.

This prints the modified result to the terminal without changing the file.

Once satisfied, run:

```bash
sudo sed -i 's/Text/Sonar/g' /root/nautilus.xml
```

---

## Verification

After editing, verify that the old string no longer exists:

```bash
sudo grep -n 'Text' /root/nautilus.xml
```

If nothing is returned, there are no remaining matches.

Then confirm the replacement:

```bash
sudo grep -n 'Sonar' /root/nautilus.xml
```

You can also count occurrences:

```bash
sudo grep -o 'Sonar' /root/nautilus.xml | wc -l
```

---

## Optional Backup Before Editing

When working on important files, create a backup first.

With GNU `sed`, you can do this automatically:

```bash
sudo sed -i.bak 's/Text/Sonar/g' /root/nautilus.xml
```

This modifies the original file and creates:

```text
/root/nautilus.xml.bak
```

That gives you a quick rollback option.

---

## Alternative Delimiters

`sed` does not require `/` as the delimiter.

This is useful when replacing filesystem paths.

Instead of:

```bash
sed -i 's/\/old\/path/\/new\/path/g' file
```

you can write:

```bash
sed -i 's|/old/path|/new/path|g' file
```

This is much easier to read.

---

## Case Sensitivity

`sed` is case-sensitive by default.

These are treated as different strings:

```text
Text
text
TEXT
```

So:

```bash
sed -i 's/Text/Sonar/g' file
```

will not replace `text`.

With GNU `sed`, a case-insensitive replacement can be written as:

```bash
sed -i 's/Text/Sonar/gI' file
```

---

## Common Mistakes

### Forgetting `-i`

This:

```bash
sed 's/Text/Sonar/g' file
```

only prints the result.

It does not change the file.

To modify the file:

```bash
sed -i 's/Text/Sonar/g' file
```

---

### Forgetting `g`

This:

```bash
sed -i 's/Text/Sonar/' file
```

replaces only the first occurrence per line.

To replace all occurrences:

```bash
sed -i 's/Text/Sonar/g' file
```

---

### Forgetting Permissions

Trying to modify files under `/root` as a normal user usually fails.

Use:

```bash
sudo
```

when required.

---

## Cheat Sheet

```bash
# Preview a replacement
sed 's/old/new/g' file

# Modify file in place
sed -i 's/old/new/g' file

# Replace only first occurrence per line
sed -i 's/old/new/' file

# Replace all occurrences per line
sed -i 's/old/new/g' file

# Create a backup while editing
sed -i.bak 's/old/new/g' file

# Use alternate delimiter
sed -i 's|/old/path|/new/path|g' file

# Verify old string is gone
grep -n 'old' file

# Verify new string exists
grep -n 'new' file
```

---

## Interview Questions

### What does `sed` stand for?

Stream editor.

### What does `-i` do?

It edits the target file in place.

### What does `s` mean in a `sed` expression?

It means substitute.

### What does the `g` flag do?

It replaces all matching occurrences on each line.

### What happens if `g` is omitted?

Only the first match on each line is replaced.

### How can you preview a replacement without modifying the file?

Run the same `sed` command without `-i`.

### Why might you use `|` instead of `/` as a delimiter?

It makes replacements involving filesystem paths easier to read.

---

## Final Solution

```bash
sudo sed -i 's/Text/Sonar/g' /root/nautilus.xml
```

Verification:

```bash
sudo grep -n 'Text' /root/nautilus.xml
sudo grep -n 'Sonar' /root/nautilus.xml
```

---

## Key Takeaway

For a simple global in-place string replacement, the core pattern is:

```bash
sed -i 's/OLD/NEW/g' file
```

This is one of the most useful command-line text manipulation patterns in Linux administration, shell scripting, and DevOps workflows.
