# 📍 pwd

> Displaying the current working directory.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Logical vs Physical Path](#logical-vs-physical-path)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `pwd` | Show current directory |
| `pwd -L` | Show logical path |
| `pwd -P` | Show physical path |

---

## Overview

`pwd` means:

```text
Print Working Directory
```

Your shell is always located somewhere in the Linux filesystem.

To see the current location:

```bash
pwd
```

Example:

```text
/home/alice/projects
```

---

## Basic Usage

Run:

```bash
pwd
```

Example:

```bash
cd /var/log
pwd
```

Output:

```text
/var/log
```

This is especially useful after moving through several directories.

---

## Logical vs Physical Path

Normally:

```bash
pwd
```

shows the logical path.

You can explicitly request it:

```bash
pwd -L
```

To resolve symbolic links and show the physical location:

```bash
pwd -P
```

Example:

```text
/home/alice/current
```

may be a symbolic link to:

```text
/opt/app/releases/v2
```

Then:

```bash
pwd -L
```

might show:

```text
/home/alice/current
```

while:

```bash
pwd -P
```

may show:

```text
/opt/app/releases/v2
```

---

## Practical Examples

### Confirm Current Location

```bash
pwd
```

before running a command such as:

```bash
rm
cp
mv
```

This helps avoid acting in the wrong directory.

---

### Use in a Script

Store the current directory:

```bash
CURRENT_DIR=$(pwd)

echo "$CURRENT_DIR"
```

Bash also provides:

```bash
echo "$PWD"
```

---

## Common Pitfalls

### Assuming Your Location

Before destructive commands, always verify:

```bash
pwd
ls
```

### Confusing `PWD` with `pwd`

This:

```bash
pwd
```

is a command.

This:

```bash
$PWD
```

is a shell variable.

Example:

```bash
echo "$PWD"
```

---

## Related Topics

- `ls.md`
- `cd.md`
- `../../02-File-System/absolute-vs-relative-paths.md`

---

## Conclusion

`pwd` answers one of the most important Linux questions:

```text
Where am I?
```

Use it frequently when navigating or before modifying files.