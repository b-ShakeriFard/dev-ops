# 🔄 source

> Loading shell configuration or script content into the current shell session.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Basic Usage](#basic-usage)
- [Why source Matters](#why-source-matters)
- [source vs Running a Script](#source-vs-running-a-script)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `source FILE` | Load file into current shell |
| `. FILE` | POSIX shorthand for `source` |
| `source ~/.bashrc` | Reload Bash configuration |
| `source ./env.sh` | Load variables/functions |
| `bash script.sh` | Run script in child shell |

---

## Overview

Normally, when you run:

```bash
bash script.sh
```

the script runs in a **child shell**.

Changes made there usually do not affect your current shell.

`source` is different:

```bash
source script.sh
```

It executes the file inside the **current shell**.

---

## Basic Usage

Syntax:

```bash
source FILE
```

Example:

```bash
source ~/.bashrc
```

Equivalent shorthand:

```bash
. ~/.bashrc
```

---

## Why source Matters

Suppose:

```bash
echo 'export APP_ENV=production' > env.sh
```

Run normally:

```bash
bash env.sh
```

Then:

```bash
echo "$APP_ENV"
```

may show nothing.

Instead:

```bash
source env.sh
```

Now:

```bash
echo "$APP_ENV"
```

shows:

```text
production
```

---

## source vs Running a Script

```text
bash script.sh
      ↓
Child shell
      ↓
Changes disappear when script ends
```

```text
source script.sh
      ↓
Current shell
      ↓
Variables/functions remain available
```

This distinction is extremely important.

---

## Practical Examples

Reload `.bashrc` after editing:

```bash
source ~/.bashrc
```

Load environment variables:

```bash
source ./environment.sh
```

Load shell functions:

```bash
source ./functions.sh
```

Example file:

```bash
hello() {
    echo "Hello!"
}
```

Then:

```bash
source functions.sh
hello
```

---

## Common Pitfalls

### Sourcing Untrusted Files

Because `source` executes commands in your current shell:

```bash
source unknown.sh
```

can modify variables, aliases, functions, directories, or run arbitrary commands.

Only source files you trust.

### Forgetting the Path

If the file is in the current directory:

```bash
source ./env.sh
```

is clearer than relying on PATH lookup.

### Expecting source to Start a New Shell

It does the opposite.

It deliberately runs inside the existing shell.

---

## Related Topics

- `environment-variables.md`
- `export.md`
- `path.md`
- `shell-startup-files.md`

---

## Conclusion

Use `source` when shell changes must remain active afterward.

The classic example is:

```bash
source ~/.bashrc
```

Mental model:

```text
Run normally → child shell
source        → current shell
```