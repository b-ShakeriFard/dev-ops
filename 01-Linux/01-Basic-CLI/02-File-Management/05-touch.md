# ✨ touch

> Creating empty files and updating file timestamps in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Files](#creating-files)
- [Updating Timestamps](#updating-timestamps)
- [Useful Options](#useful-options)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `touch FILE` | Create file if missing |
| `touch FILE1 FILE2` | Create multiple files |
| `touch EXISTING` | Update timestamps |
| `touch -c FILE` | Do not create if missing |
| `touch -a FILE` | Update access time |
| `touch -m FILE` | Update modification time |
| `touch -t TIME FILE` | Set specific timestamp |

---

## Overview

`touch` is commonly used to create empty files:

```bash
touch notes.txt
```

If the file does not exist, it is created.

If it already exists, `touch` updates its timestamps without changing its contents.

---

## Creating Files

Create one file:

```bash
touch file.txt
```

Create several:

```bash
touch file1.txt file2.txt file3.txt
```

Verify:

```bash
ls -l
```

Create numbered files:

```bash
touch file{1..5}.txt
```

Result:

```text
file1.txt
file2.txt
file3.txt
file4.txt
file5.txt
```

---

## Updating Timestamps

Run:

```bash
touch existing.txt
```

This updates the file's access and modification timestamps.

Check:

```bash
stat existing.txt
```

Typical timestamps include:

```text
Access
Modify
Change
```

---

## Useful Options

Update only access time:

```bash
touch -a file.txt
```

Update only modification time:

```bash
touch -m file.txt
```

Do not create the file if it does not exist:

```bash
touch -c file.txt
```

Set a specific timestamp:

```bash
touch -t 202609131200 file.txt
```

Format:

```text
YYYYMMDDhhmm
```

---

## Practical Examples

Create log placeholder:

```bash
touch application.log
```

Create configuration files:

```bash
touch app.conf database.conf
```

Create project files:

```bash
mkdir project
touch project/{README.md,config.yml,app.log}
```

Update modification time:

```bash
touch -m README.md
```

---

## Common Pitfalls

### Assuming touch Clears a File

This:

```bash
touch file.txt
```

does **not** erase existing content.

To empty a file:

```bash
> file.txt
```

### Permission Denied

You need write permission on the directory to create a new file.

Check:

```bash
ls -ld .
```

### Timestamp Changes

Running `touch` on an existing file changes timestamps even though its contents remain unchanged.

---

## Related Topics

- `mkdir.md`
- `cp.md`
- `mv.md`
- `../../../02-File-System/file-types.md`

---

## Conclusion

The most common use is simply:

```bash
touch FILE
```

It is useful for creating placeholder files, testing workflows, and managing timestamps.