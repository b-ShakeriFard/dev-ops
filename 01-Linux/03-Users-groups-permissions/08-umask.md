# 🎭 umask - Default Permissions

> Controlling the default permissions assigned to newly created files and directories.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [How umask Works](#how-umask-works)
- [Common Values](#common-values)
- [Practical Examples](#practical-examples)
- [Related Topics](#related-topics)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `umask` | Show current mask |
| `umask 022` | Set common default mask |
| `umask 027` | Restrict access for others |
| `umask 077` | Private files and directories |

---

## Overview

`umask` controls which permissions are **removed** when new files and directories are created.

Default starting permissions are typically:

```text
Files       → 666
Directories → 777
```

`umask` subtracts permissions from these defaults.

---

## How umask Works

Example:

```text
Default file permissions: 666
umask:                    022
Result:                   644
```

So a new file becomes:

```text
rw-r--r--
```

For directories:

```text
777 - 022 = 755
```

Result:

```text
rwxr-xr-x
```

---

## Common Values

| umask | Files | Directories | Typical Use |
|---|---|---|---|
| `022` | `644` | `755` | General systems |
| `027` | `640` | `750` | More restrictive servers |
| `077` | `600` | `700` | Private user data |

---

## Practical Examples

Check current value:

```bash
umask
```

Set:

```bash
umask 027
```

Create a file:

```bash
touch test.txt
```

Check:

```bash
ls -l test.txt
```

Expected:

```text
-rw-r-----
```

Create directory:

```bash
mkdir testdir
```

Expected:

```text
drwxr-x---
```

---

## Persistent umask

`umask` set in the shell is usually temporary.

User-level configuration may be placed in files such as:

```text
~/.bashrc
~/.profile
```

Example:

```bash
umask 027
```

System-wide defaults depend on the distribution and login configuration.

---

## Important Detail

`umask` does not directly assign permissions.

It removes permissions from the creation defaults.

Think:

```text
Default Permissions
        ↓
Apply umask
        ↓
Final Permissions
```

---

## Common Pitfall

A common mistake is thinking:

```text
umask 022
```

means permissions become:

```text
022
```

It actually means those permission bits are removed.

---

## Related Topics

- `chmod.md`
- `ownership.md`
- `special-permissions.md`

---

## Conclusion

`umask` controls the default access level of newly created files and directories.

The common mental model is:

```text
Files       → 666 - umask
Directories → 777 - umask
```

For private environments, a stricter mask such as `027` or `077` is often appropriate.