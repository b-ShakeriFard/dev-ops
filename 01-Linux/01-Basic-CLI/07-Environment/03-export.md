# 📤 export

> Making shell variables available to child processes.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Creating Exported Variables](#creating-exported-variables)
- [Exporting Existing Variables](#exporting-existing-variables)
- [Testing Inheritance](#testing-inheritance)
- [Practical Examples](#practical-examples)
- [Common Pitfalls](#common-pitfalls)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `export VAR=value` | Create and export variable |
| `VAR=value` | Create shell variable only |
| `export VAR` | Export existing variable |
| `export -p` | Show exported variables |
| `unset VAR` | Remove variable |
| `env VAR=value command` | Temporary variable for one command |

---

## Overview

A normal shell variable:

```bash
APP_ENV="production"
```

exists in the current shell.

A child process normally does not inherit it.

Export it:

```bash
export APP_ENV
```

Now child processes can access it.

Mental model:

```text
Shell Variable
     ↓
   export
     ↓
Environment Variable
     ↓
Child Processes
```

---

## Creating Exported Variables

Create and export in one command:

```bash
export APP_ENV="production"
```

Check:

```bash
printenv APP_ENV
```

Output:

```text
production
```

---

## Exporting Existing Variables

Create:

```bash
PORT=8080
```

Export later:

```bash
export PORT
```

This keeps the existing value while making it inheritable.

---

## Testing Inheritance

Create a shell variable:

```bash
NAME="alice"
```

Start a child shell:

```bash
bash -c 'echo "$NAME"'
```

Normally, nothing appears.

Now:

```bash
export NAME
```

Try again:

```bash
bash -c 'echo "$NAME"'
```

Output:

```text
alice
```

---

## Practical Examples

Application configuration:

```bash
export APP_ENV="production"
export APP_PORT=8080
```

Proxy configuration:

```bash
export HTTPS_PROXY="http://proxy.example.com:8080"
```

Add a directory to PATH:

```bash
export PATH="$PATH:/opt/myapp/bin"
```

Run a command with a temporary value instead:

```bash
env DEBUG=true ./app.sh
```

---

## Common Pitfalls

### Assuming export Makes Variables Permanent

This:

```bash
export APP_ENV="production"
```

usually lasts only for the current shell session.

For persistence, use an appropriate shell startup file.

### Overwriting PATH

Dangerous:

```bash
export PATH="/opt/myapp/bin"
```

This may remove access to normal system commands.

Prefer:

```bash
export PATH="$PATH:/opt/myapp/bin"
```

### Parent Processes Do Not Inherit Backwards

A child process can inherit from its parent.

But changes inside the child do not modify the parent's environment.

---

## Related Topics

- `environment-variables.md`
- `env-printenv.md`
- `path.md`
- `source.md`
- `shell-startup-files.md`

---

## Conclusion

The key difference is:

```bash
VAR=value
```

creates a shell variable.

```bash
export VAR=value
```

creates a value that child processes can inherit.

That distinction is fundamental to Linux shells, scripts, CI/CD, and application configuration.