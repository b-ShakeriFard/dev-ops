# 🐞 Bash Debugging

> Finding syntax errors, tracing execution, and making scripts safer.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Syntax Checking](#syntax-checking)
- [Execution Tracing](#execution-tracing)
- [Safer Bash Settings](#safer-bash-settings)
- [Common Debugging Techniques](#common-debugging-techniques)
- [Practical Example](#practical-example)

---

## Quick Cheat Sheet

| Command / Option | Purpose |
|---|---|
| `bash -n script.sh` | Check syntax |
| `bash -x script.sh` | Trace execution |
| `set -x` | Enable tracing |
| `set +x` | Disable tracing |
| `set -e` | Exit on many command failures |
| `set -u` | Error on unset variables |
| `set -o pipefail` | Detect pipeline failures |
| `shellcheck script.sh` | Static analysis |

---

## Overview

Debugging Bash usually means checking:

```text
Syntax
Variables
Exit codes
Command output
Execution flow
```

Before changing a script randomly, first identify where it fails.

---

## Syntax Checking

Check without executing:

```bash
bash -n script.sh
```

If there is no output, Bash found no syntax error.

Typical errors include:

```text
Missing fi
Missing done
Broken quotes
Missing }
```

---

## Execution Tracing

Run with tracing:

```bash
bash -x script.sh
```

Bash prints commands as they execute.

You can enable tracing inside a script:

```bash
set -x

command1
command2

set +x
```

This is useful for debugging only part of a script.

---

## Safer Bash Settings

A common pattern is:

```bash
set -euo pipefail
```

Meaning:

```text
-e        exit on many unhandled failures
-u        fail on unset variables
pipefail  detect failures inside pipelines
```

These options help expose hidden problems, but they can change script behavior, so use them deliberately.

---

## Common Debugging Techniques

Print variable values:

```bash
echo "DEBUG: USER=$USER"
```

Check exit status:

```bash
command
echo "$?"
```

Check whether a variable exists:

```bash
echo "${VAR:-NOT_SET}"
```

Check script arguments:

```bash
printf 'Arguments: %s\n' "$#"
```

---

## Practical Example

Problem:

```bash
#!/bin/bash

FILE=$1

cat "$FILE"
echo "Backup complete"
```

Improved:

```bash
#!/bin/bash

set -u

if [ "$#" -ne 1 ]; then
    echo "Usage: $0 FILE"
    exit 1
fi

FILE="$1"

if ! cat "$FILE"; then
    echo "Failed to read $FILE"
    exit 1
fi

echo "Backup complete"
```

---

## ShellCheck

If installed:

```bash
shellcheck script.sh
```

It can detect:

- Unquoted variables
- Suspicious syntax
- Unused variables
- Common Bash mistakes

---

## Related Topics

- `exit-codes.md`
- `conditionals.md`
- `arguments.md`
- `practical-scripts.md`

---

## Conclusion

A good Bash debugging workflow is:

```text
bash -n
   ↓
bash -x
   ↓
Check variables
   ↓
Check exit codes
   ↓
Fix root cause
```

For reliable scripts, combine testing, tracing, and careful error handling.