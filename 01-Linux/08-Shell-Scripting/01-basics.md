# 🐚 Bash Script Basics

> Creating, executing, and understanding simple Bash scripts.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [The Shebang](#the-shebang)
- [Creating a Script](#creating-a-script)
- [Running a Script](#running-a-script)
- [Comments](#comments)
- [Practical Example](#practical-example)

---

## Quick Cheat Sheet

| Syntax / Command | Purpose |
|---|---|
| `#!/bin/bash` | Use Bash interpreter |
| `chmod +x script.sh` | Make script executable |
| `./script.sh` | Run executable script |
| `bash script.sh` | Run script with Bash |
| `# comment` | Add comment |
| `echo "text"` | Print output |

---

## Overview

A shell script is simply a text file containing Linux commands.

Example:

```bash
#!/bin/bash

echo "Hello Linux"
date
uptime
```

Instead of typing each command manually, Bash executes them in sequence.

---

## The Shebang

The first line is commonly:

```bash
#!/bin/bash
```

This tells Linux which interpreter should execute the script.

A more portable alternative is:

```bash
#!/usr/bin/env bash
```

---

## Creating a Script

Create:

```bash
nano hello.sh
```

Add:

```bash
#!/bin/bash

echo "Hello!"
```

Save the file.

---

## Running a Script

Using Bash directly:

```bash
bash hello.sh
```

Or make it executable:

```bash
chmod +x hello.sh
```

Then run:

```bash
./hello.sh
```

The `./` means:

```text
Run the file from the current directory
```

---

## Comments

Single-line comments begin with:

```bash
#
```

Example:

```bash
# Check system uptime
uptime
```

Comments help explain why a script performs a task.

---

## Practical Example

Create:

```bash
#!/bin/bash

echo "=== System Check ==="

hostname
date
uptime
df -h
```

Make executable:

```bash
chmod +x system-check.sh
```

Run:

```bash
./system-check.sh
```

---

## Common Pitfalls

### Permission Denied

Example:

```text
Permission denied
```

Fix:

```bash
chmod +x script.sh
```

### Wrong Interpreter

Check the first line:

```bash
head -1 script.sh
```

### Windows Line Endings

Scripts copied from Windows may fail with errors involving:

```text
^M
```

Convert with:

```bash
dos2unix script.sh
```

---

## Related Topics

- `variables.md`
- `input-output.md`
- `arguments.md`
- `debugging.md`

---

## Conclusion

The basic Bash script workflow is:

```text
Create file
   ↓
Add shebang
   ↓
Add commands
   ↓
chmod +x
   ↓
Run script
```

Once this is comfortable, scripting becomes mostly about adding variables and logic.