# 🚀 Shell Startup Files

> Understanding which Bash configuration files are loaded when a shell starts.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Login Shells](#login-shells)
- [Interactive Shells](#interactive-shells)
- [Common Startup Files](#common-startup-files)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| File | Typical Purpose |
|---|---|
| `/etc/profile` | System-wide login shell config |
| `~/.bash_profile` | User login shell config |
| `~/.bash_login` | Alternative login config |
| `~/.profile` | Login config used by many shells |
| `~/.bashrc` | Interactive non-login Bash config |
| `/etc/bashrc` | System-wide Bash config on some distros |

---

## Overview

Bash can start in different ways.

The most important distinction is:

```text
Login shell
vs
Interactive non-login shell
```

Different startup files may be loaded depending on how the shell starts.

---

## Login Shells

A login shell may be started when:

```text
Logging into a system
SSH session
Console login
```

Bash typically reads:

```text
/etc/profile
```

Then the first available of:

```text
~/.bash_profile
~/.bash_login
~/.profile
```

A common pattern is for `~/.bash_profile` to load `~/.bashrc`.

Example:

```bash
if [ -f ~/.bashrc ]; then
    source ~/.bashrc
fi
```

---

## Interactive Shells

Opening a normal terminal usually starts an interactive non-login Bash shell.

It typically reads:

```text
~/.bashrc
```

This is commonly where users place:

```text
Aliases
Functions
Prompt settings
PATH changes
Environment setup
```

---

## Common Startup Files

System-wide:

```text
/etc/profile
/etc/bashrc
/etc/bash.bashrc
```

User-specific:

```text
~/.bash_profile
~/.bash_login
~/.profile
~/.bashrc
```

Exact behavior varies slightly between distributions.

---

## Practical Examples

Add a personal directory to PATH:

```bash
export PATH="$PATH:$HOME/bin"
```

Place it in:

```text
~/.bashrc
```

Then reload:

```bash
source ~/.bashrc
```

Create an alias:

```bash
alias ll='ls -lah'
```

Add it to:

```text
~/.bashrc
```

---

## Common Pitfalls

### Editing the Wrong File

If a variable works in a terminal but not during login, the relevant startup file may differ.

### Forgetting to Reload

After editing:

```bash
~/.bashrc
```

reload with:

```bash
source ~/.bashrc
```

### Duplicate PATH Entries

Repeated lines such as:

```bash
export PATH="$PATH:$HOME/bin"
```

can produce duplicate PATH entries across sessions.

### Distribution Differences

For example:

```text
Debian/Ubuntu → /etc/bash.bashrc
RHEL/Rocky    → /etc/bashrc
```

---

## Related Topics

- `environment-variables.md`
- `export.md`
- `path.md`
- `source.md`

---

## Conclusion

The most important rule to remember is:

```text
Login shell
   ↓
profile files

Interactive Bash shell
   ↓
~/.bashrc
```

Understanding startup files explains where aliases, PATH changes, functions, and environment settings should live.