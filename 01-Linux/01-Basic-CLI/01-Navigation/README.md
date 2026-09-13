# 🧭 Navigation

> Moving around the Linux filesystem with `pwd`, `ls`, and `cd`.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Core Commands](#core-commands)
- [Path Concepts](#path-concepts)
- [Practical Workflow](#practical-workflow)
- [Files in This Folder](#files-in-this-folder)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `pwd` | Show current directory |
| `ls` | List directory contents |
| `ls -l` | Long listing |
| `ls -a` | Include hidden files |
| `cd DIR` | Change directory |
| `cd ..` | Move to parent |
| `cd ~` | Go to home directory |
| `cd -` | Return to previous directory |

---

## Overview

Linux navigation is based on directories arranged in a tree.

```text
/
├── etc
├── home
├── var
├── usr
└── tmp
```

Your shell is always located in one current working directory.

Check it with:

```bash
pwd
```

---

## Core Commands

### pwd

Shows where you currently are:

```bash
pwd
```

Example:

```text
/home/alice/projects
```

---

### ls

Shows directory contents:

```bash
ls
```

Useful combinations:

```bash
ls -l
ls -a
ls -lh
ls -lah
```

---

### cd

Move into a directory:

```bash
cd /var/log
```

Move up one level:

```bash
cd ..
```

Go home:

```bash
cd ~
```

Return to the previous directory:

```bash
cd -
```

---

## Path Concepts

Two path types are important:

```text
Absolute path
Relative path
```

Absolute:

```bash
cd /var/log
```

Relative:

```bash
cd ../logs
```

Special path symbols:

```text
/   → root
.   → current directory
..  → parent directory
~   → home directory
```

---

## Practical Workflow

A very common sequence is:

```bash
pwd
ls -lah
cd /var/log
ls
```

Mental model:

```text
Where am I?
    ↓
What is here?
    ↓
Where do I want to go?
```

---

## Files in This Folder

```text
01-Navigation/
├── README.md
├── pwd.md
├── ls.md
└── cd.md
```

---

## Conclusion

The three essential navigation commands are:

```bash
pwd
ls
cd
```

Master these first; nearly every Linux task starts with navigation.