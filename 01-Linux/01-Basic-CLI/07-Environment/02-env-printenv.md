# 🔎 env & printenv

> Inspecting environment variables from the Linux command line.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Using env](#using-env)
- [Using printenv](#using-printenv)
- [Filtering Output](#filtering-output)
- [Running Commands with env](#running-commands-with-env)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `env` | Show environment variables |
| `printenv` | Show environment variables |
| `printenv HOME` | Show one variable |
| `env \| sort` | Sort environment output |
| `env VAR=value command` | Run command with temporary variable |
| `env -i command` | Run with nearly empty environment |

---

## Overview

Both `env` and `printenv` can display environment variables.

Example:

```bash
env
```

Output may include:

```text
HOME=/home/alice
USER=alice
SHELL=/bin/bash
PATH=/usr/local/bin:/usr/bin:/bin
```

---

## Using env

Show the current environment:

```bash
env
```

Sort it:

```bash
env | sort
```

Search for a variable:

```bash
env | grep PATH
```

`env` can also modify the environment for a single command.

---

## Using printenv

Show all exported variables:

```bash
printenv
```

Show one variable:

```bash
printenv HOME
```

Multiple variables:

```bash
printenv USER HOME SHELL
```

This is often cleaner than:

```bash
echo "$HOME"
```

when you specifically want to inspect environment variables.

---

## Filtering Output

Find PATH-related variables:

```bash
printenv | grep PATH
```

Search case-insensitively:

```bash
env | grep -i proxy
```

This is useful for checking settings such as:

```text
HTTP_PROXY
HTTPS_PROXY
NO_PROXY
```

---

## Running Commands with env

Set a temporary variable:

```bash
env APP_ENV=test ./app.sh
```

`APP_ENV` exists only for that command and its child processes.

Example:

```bash
env NAME=alice bash -c 'echo "$NAME"'
```

Run with a minimal environment:

```bash
env -i bash
```

This is useful for testing whether a problem depends on inherited variables.

---

## Practical Examples

Check current shell:

```bash
printenv SHELL
```

Check PATH:

```bash
printenv PATH
```

Find locale settings:

```bash
env | grep '^LANG\|^LC_'
```

Test an application:

```bash
env DEBUG=true ./application
```

---

## Common Pitfalls

### Expecting Shell Variables to Appear

This:

```bash
NAME="alice"
```

may not appear in:

```bash
env
```

until exported:

```bash
export NAME
```

### Large Output

Instead of scanning everything:

```bash
env
```

use:

```bash
printenv VARIABLE
```

or pipe through `grep`.

---

## Related Topics

- `environment-variables.md`
- `export.md`
- `path.md`
- `shell-startup-files.md`

---

## Conclusion

Use:

```bash
env
```

for inspecting or modifying a command's environment.

Use:

```bash
printenv VARIABLE
```

when you want to quickly inspect a specific exported variable.