# ⚡ alias & unalias

> Creating short command shortcuts for frequently used shell commands.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Aliases](#creating-aliases)
- [Viewing Aliases](#viewing-aliases)
- [Removing Aliases](#removing-aliases)
- [Persistent Aliases](#persistent-aliases)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `alias` | Show all aliases |
| `alias NAME='COMMAND'` | Create alias |
| `alias NAME` | Show one alias |
| `unalias NAME` | Remove alias |
| `unalias -a` | Remove all aliases |
| `source ~/.bashrc` | Reload aliases |

---

## Overview

An alias is a shortcut for another command.

Example:

```bash
alias ll='ls -lah'
```

Now:

```bash
ll
```

runs:

```bash
ls -lah
```

Aliases are useful for long or frequently used commands.

---

## Creating Aliases

Basic syntax:

```bash
alias NAME='COMMAND'
```

Examples:

```bash
alias ll='ls -lah'
alias cls='clear'
alias k='kubectl'
```

You can also include options:

```bash
alias grep='grep --color=auto'
```

---

## Viewing Aliases

Show all aliases:

```bash
alias
```

Show one:

```bash
alias ll
```

Possible output:

```text
alias ll='ls -lah'
```

You can also inspect command resolution:

```bash
type ll
```

---

## Removing Aliases

Remove one alias:

```bash
unalias ll
```

Remove all aliases:

```bash
unalias -a
```

---

## Persistent Aliases

Aliases created interactively disappear when the shell session ends.

To make them persistent, place them in:

```text
~/.bashrc
```

Example:

```bash
alias ll='ls -lah'
alias gs='git status'
```

Reload:

```bash
source ~/.bashrc
```

---

## Practical Examples

Shortcut for systemctl:

```bash
alias sc='systemctl'
```

Then:

```bash
sc status nginx
```

Git shortcut:

```bash
alias gs='git status'
```

Safer copy:

```bash
alias cp='cp -i'
```

Safer move:

```bash
alias mv='mv -i'
```

---

## Common Pitfalls

### Aliases Cannot Easily Handle Arguments

This is fine:

```bash
alias ll='ls -lah'
```

But complex parameterized behavior is better implemented as a shell function.

### Alias Hides the Real Command

If:

```bash
alias ls='ls --color=auto'
```

then typing:

```bash
ls
```

actually uses the alias.

Check with:

```bash
type ls
```

Bypass alias with:

```bash
command ls
```

or:

```bash
\ls
```

### Alias Is Not Permanent

If you do not add it to a startup file, it disappears after logout.

---

## Related Topics

- `history.md`
- `echo-printf.md`
- `tee.md`
- `../07-Environment/shell-startup-files.md`

---

## Conclusion

The core pattern is:

```bash
alias NAME='COMMAND'
```

For shortcuts you use regularly, add them to:

```text
~/.bashrc
```

Aliases are excellent for convenience; use functions when you need real scripting logic.