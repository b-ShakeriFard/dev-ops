# 🐚 Shell Scripting

> Automating repetitive Linux administration tasks with Bash.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Overview](#overview)
- [Script Structure](#script-structure)
- [Why Shell Scripting Matters](#why-shell-scripting-matters)
- [Topics](#topics)

---

## Quick Cheat Sheet

| Syntax | Purpose |
|---|---|
| `#!/bin/bash` | Bash shebang |
| `chmod +x script.sh` | Make script executable |
| `./script.sh` | Run script |
| `$VAR` | Read variable |
| `$1` | First argument |
| `$?` | Previous exit code |
| `$(command)` | Command substitution |
| `if ... fi` | Conditional |
| `for ... done` | Loop |
| `function name {}` | Function |

---

## Overview

A shell script is a text file containing commands that would otherwise be entered manually.

Example:

```bash
#!/bin/bash

echo "Checking server..."

df -h
free -h
uptime
```

Run:

```bash
chmod +x health-check.sh
./health-check.sh
```

---

## Script Structure

```mermaid
flowchart LR

    A["🐚 Script"] --> B["Variables"]
    B --> C["Conditions"]
    C --> D["Loops"]
    D --> E["Functions"]
    E --> F["Commands"]
    F --> G["Exit Code"]
```

A typical script may:

```text
Read input
   ↓
Run commands
   ↓
Check conditions
   ↓
Repeat tasks
   ↓
Return success/failure
```

---

## Why Shell Scripting Matters

Shell scripting turns manual administration into automation.

Examples:

```text
Check disk space
Restart failed services
Create users
Clean logs
Verify ports
Run backups
Check HTTP endpoints
```

This is especially useful in:

- Linux administration
- CI/CD pipelines
- Kubernetes automation
- Server provisioning
- Troubleshooting

---

## Topics

```text
08-Shell-Scripting/
├── README.md
├── basics.md
├── variables.md
├── input-output.md
├── conditionals.md
├── loops.md
├── functions.md
├── arguments.md
├── exit-codes.md
├── command-substitution.md
├── pipes-redirection.md
├── arrays.md
├── debugging.md
└── practical-scripts.md
```

---

## Key Mental Model

```text
Linux Commands
      ↓
Script
      ↓
Logic
      ↓
Automation
```

---

## Related Chapters

- `../01-Basic-CLI/`
- `../05-Processes-Systemd/`
- `../06-Networking/`
- `../07-Storage/`

---

## Conclusion

Shell scripting combines Linux commands with simple programming logic.

The goal is not complex software development.

The goal is:

```text
Automate repeatable Linux tasks
reliably and safely.
```