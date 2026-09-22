# Linux Process Limit Adjustment

## Overview

This KodeKloud challenge focused on limiting how many processes a specific Linux user can run.

Target user:

```text
nfsuser
```

Required limits:

```text
soft nproc = 1025
hard nproc = 2026
```

The persistent configuration is commonly stored in:

```bash
/etc/security/limits.conf
```

---

## Why Process Limits Matter

A user that creates too many processes can consume CPU and memory, degrade application performance, or even destabilize a server.

Linux allows administrators to apply per-user resource limits to reduce this risk.

---

## `limits.conf` Syntax

The general format is:

```text
<domain>   <type>   <item>   <value>
```

For this task:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   2026
```

### Field meanings

| Field | Meaning |
|---|---|
| `nfsuser` | User the rule applies to |
| `soft` / `hard` | Limit type |
| `nproc` | Maximum number of processes |
| `1025` / `2026` | Limit values |

---

## Soft vs Hard Limits

The **soft limit** is the normal active limit for a user's session.

The **hard limit** is the maximum ceiling the user can raise the soft limit to.

In this challenge:

```text
soft = 1025
hard = 2026
```

So the user normally gets a process limit of 1025 and cannot raise it above 2026 without administrative privileges.

---

## Configure the Limit

Edit:

```bash
sudo vi /etc/security/limits.conf
```

Add:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   2026
```

Save and exit.

---

## Verify the Configuration

```bash
grep nfsuser /etc/security/limits.conf
```

Expected:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   2026
```

---

## The Mistake in the First Attempt

The initial configuration accidentally used:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   1025
```

The hard limit should have been:

```text
2026
```

The corrected line was:

```text
nfsuser   hard   nproc   2026
```

This is a good reminder to verify configuration values before submitting a task.

---

## Checking Runtime Limits

To display the current maximum process limit for the active shell:

```bash
ulimit -u
```

To display all shell limits:

```bash
ulimit -a
```

Changes in `/etc/security/limits.conf` normally apply to **new login sessions**, so a user may need to log out and back in before seeing the new value.

---

## Other Common Limit Items

`nproc` is only one resource that can be controlled.

Examples include:

```text
nofile     maximum open files
core       maximum core file size
memlock    maximum locked memory
maxlogins  maximum simultaneous logins
```

---

## Useful Commands

```bash
# Edit persistent limits
sudo vi /etc/security/limits.conf

# Verify nfsuser entries
grep nfsuser /etc/security/limits.conf

# Current process limit
ulimit -u

# All current shell limits
ulimit -a
```

---

## Common Mistakes

### Using the wrong hard limit

Wrong:

```text
nfsuser soft nproc 1025
nfsuser hard nproc 1025
```

Correct:

```text
nfsuser soft nproc 1025
nfsuser hard nproc 2026
```

### Confusing `nproc` with another resource

`nproc` controls the number of processes, not open files or login sessions.

### Testing from an old session

Persistent PAM limits generally apply when a new session is created.

### Forgetting `/etc/security/limits.d/`

Some systems also contain additional resource-limit rules under:

```bash
/etc/security/limits.d/
```

These may affect the effective configuration.

---

## Cheat Sheet

```bash
sudo vi /etc/security/limits.conf
```

Add:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   2026
```

Verify:

```bash
grep nfsuser /etc/security/limits.conf
```

Check runtime value:

```bash
ulimit -u
```

---

## Interview Questions

### What does `nproc` control?

The maximum number of processes available to a user.

### What is a soft limit?

The normal currently enforced resource limit.

### What is a hard limit?

The maximum ceiling to which a user may raise the soft limit.

### Where are persistent user resource limits configured?

Usually in:

```bash
/etc/security/limits.conf
```

and files under:

```bash
/etc/security/limits.d/
```

### How can you view the current shell process limit?

```bash
ulimit -u
```

### Do `limits.conf` changes affect existing sessions immediately?

Usually not. They normally apply to newly created login sessions.

---

## Key Takeaway

The final configuration was:

```text
nfsuser   soft   nproc   1025
nfsuser   hard   nproc   2026
```

This challenge demonstrates how Linux resource limits can protect a server from excessive per-user process consumption and help maintain system stability.
