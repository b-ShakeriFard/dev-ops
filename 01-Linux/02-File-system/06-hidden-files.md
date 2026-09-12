# 👻 Hidden Files in Linux

> Understanding dotfiles and hidden directories in Linux.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Common Examples](#common-examples)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `ls -a` | Show hidden files |
| `ls -la` | Show hidden files with details |
| `find . -name ".*"` | Find hidden files |
| `du -sh .[^.]*` | Check hidden file/directory sizes |
| `mv FILE .FILE` | Make a file hidden |

---

## Overview

In Linux, a file or directory is considered hidden when its name starts with:

```text
.
```

Examples:

```text
.bashrc
.gitconfig
.ssh
.config
```

Hidden files are often called:

```text
dotfiles
```

They are commonly used for:

- User configuration
- Application settings
- Shell configuration
- SSH keys
- Git configuration

---

## Common Examples

### `.bashrc`

Shell configuration:

```text
~/.bashrc
```

### `.ssh`

SSH configuration and keys:

```text
~/.ssh/
```

Typical contents:

```text
authorized_keys
config
id_ed25519
known_hosts
```

### `.gitconfig`

Git user configuration:

```text
~/.gitconfig
```

### `.config`

Many desktop applications store configuration under:

```text
~/.config/
```

---

## Practical Examples

Normal listing:

```bash
ls
```

Hidden files are omitted.

Show everything:

```bash
ls -a
```

Detailed view:

```bash
ls -la
```

Example:

```text
.bashrc
.profile
.ssh
.gitconfig
```

---

## Create a Hidden File

Create:

```bash
touch .example
```

Verify:

```bash
ls -la
```

Make an existing file hidden:

```bash
mv config config.old
mv config.old .config
```

---

## Common Pitfalls

### Hidden Does Not Mean Secure

A hidden file is only hidden from normal directory listings.

It is **not protected**.

Security still depends on permissions:

```bash
ls -l ~/.ssh
```

---

### Hidden Files Can Consume Disk Space

A directory may look small with:

```bash
ls
```

while large hidden files exist.

Check:

```bash
du -sh .[^.]*
```

---

## Related Topics

- `file-types.md`
- `permissions-basics.md`
- `links.md`

---

## Conclusion

Linux hidden files are simply files whose names begin with:

```text
.
```

They are heavily used for configuration, but hidden does not mean private or secure.