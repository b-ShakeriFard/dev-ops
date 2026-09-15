# Restricting Cron Access with `cron.allow` and `cron.deny`

## Overview

This challenge focused on controlling which Linux users are allowed to create or modify scheduled cron jobs.

The requirement was:

- allow user `james` to use `crontab`
- deny user `rod` from using `crontab`

This is handled with two access-control files:

```bash
/etc/cron.allow
/etc/cron.deny
```

These files are used by `crontab` to decide whether a user is permitted to manage personal cron jobs.

---

## What Is Cron?

`cron` is the standard Linux scheduler used to run commands automatically at specific times or intervals.

Examples include:

- running backups every night
- cleaning temporary files
- rotating logs
- running monitoring scripts
- executing maintenance jobs

A user's personal cron jobs are typically managed with:

```bash
crontab -e
```

and listed with:

```bash
crontab -l
```

Because scheduled jobs can execute arbitrary commands, controlling who can use `crontab` is important from a security perspective.

---

## The Two Access-Control Files

### `/etc/cron.allow`

Users listed in this file are allowed to use `crontab`.

Example:

```text
james
```

### `/etc/cron.deny`

Users listed in this file are denied access to `crontab`.

Example:

```text
rod
```

The exact behavior can vary slightly between cron implementations, but on common Linux systems the usual logic is:

1. if `/etc/cron.allow` exists, only listed users are allowed
2. otherwise, `/etc/cron.deny` is checked
3. users listed in `/etc/cron.deny` are blocked

For a lab task, it is best to configure exactly what the challenge asks.

---

## Step 1 — Check Existing Configuration

Check whether the files already exist:

```bash
sudo ls -l /etc/cron.allow /etc/cron.deny
```

You can also inspect them individually:

```bash
sudo cat /etc/cron.allow
sudo cat /etc/cron.deny
```

In this challenge, `/etc/cron.allow` did not initially exist.

---

## Step 2 — Allow `james`

Create or append `james` to `/etc/cron.allow`:

```bash
echo "james" | sudo tee -a /etc/cron.allow
```

### Breakdown

```text
echo "james"   -> outputs the username
|              -> pipes the output
sudo tee -a    -> appends to a root-owned file
/etc/cron.allow
```

Why not simply use:

```bash
sudo echo "james" >> /etc/cron.allow
```

Because the shell performs `>>` redirection before `sudo` applies to `echo`.

A normal user may therefore still get:

```text
Permission denied
```

Using `sudo tee -a` solves this cleanly.

---

## Step 3 — Deny `rod`

Append `rod` to `/etc/cron.deny`:

```bash
echo "rod" | sudo tee -a /etc/cron.deny
```

This explicitly records that `rod` must not be allowed to manage cron jobs.

---

## Step 4 — Verify

Check the allow list:

```bash
sudo cat /etc/cron.allow
```

Expected:

```text
james
```

Check the deny list:

```bash
sudo cat /etc/cron.deny
```

Expected:

```text
rod
```

---

## Final Commands

```bash
echo "james" | sudo tee -a /etc/cron.allow
echo "rod" | sudo tee -a /etc/cron.deny

sudo cat /etc/cron.allow
sudo cat /etc/cron.deny
```

---

## Why `tee -a`?

The `tee` command reads standard input and writes it to a file.

The `-a` option means:

```text
append
```

So:

```bash
echo "james" | sudo tee -a /etc/cron.allow
```

adds the username without overwriting existing contents.

Without `-a`:

```bash
echo "james" | sudo tee /etc/cron.allow
```

the file would be overwritten.

That could accidentally remove existing authorized users.

---

## Important Security Concept

Cron jobs can execute commands automatically under a user's identity.

That means access to `crontab` can effectively grant a user the ability to:

- run scripts repeatedly
- execute unattended commands
- launch programs after reboot or at scheduled times
- consume resources
- modify files the user can access

Restricting cron access is therefore part of Linux hardening.

---

## Common Mistakes

### 1. Overwriting the File

This:

```bash
echo "james" | sudo tee /etc/cron.allow
```

replaces the file contents.

Safer:

```bash
echo "james" | sudo tee -a /etc/cron.allow
```

---

### 2. Using Redirection Incorrectly with `sudo`

This may fail:

```bash
sudo echo "james" >> /etc/cron.allow
```

because `>>` is handled by the current shell.

Use:

```bash
echo "james" | sudo tee -a /etc/cron.allow
```

---

### 3. Editing the Wrong File

Be careful not to confuse:

```text
/etc/cron.allow
/etc/cron.deny
```

with:

```text
/etc/crontab
```

`/etc/crontab` contains scheduled jobs.

`cron.allow` and `cron.deny` control who may use `crontab`.

---

### 4. Creating Duplicate Entries

Repeated use of:

```bash
tee -a
```

can add duplicate usernames.

Check first if needed:

```bash
grep '^james$' /etc/cron.allow
```

A duplicate generally does not break functionality, but clean configuration is preferable.

---

## Useful Commands

### Edit current user's crontab

```bash
crontab -e
```

### List current user's cron jobs

```bash
crontab -l
```

### List another user's cron jobs

```bash
sudo crontab -u username -l
```

### Edit another user's crontab

```bash
sudo crontab -u username -e
```

### Check cron access control

```bash
sudo cat /etc/cron.allow
sudo cat /etc/cron.deny
```

---

## Cheat Sheet

```bash
# Allow a user
echo "username" | sudo tee -a /etc/cron.allow

# Deny a user
echo "username" | sudo tee -a /etc/cron.deny

# Verify allow list
sudo cat /etc/cron.allow

# Verify deny list
sudo cat /etc/cron.deny

# Edit personal crontab
crontab -e

# List personal crontab
crontab -l
```

---

## Interview Questions

### What is `/etc/cron.allow`?

It is an access-control file that specifies users allowed to use `crontab`.

### What is `/etc/cron.deny`?

It specifies users who are denied access to `crontab`.

### Why is cron access security-sensitive?

Because cron allows users to schedule commands and scripts to execute automatically.

### Why use `sudo tee -a` instead of `sudo echo >> file`?

Because shell redirection is performed before `sudo`, while `tee` itself runs with elevated privileges.

### What does `-a` mean with `tee`?

It means append instead of overwrite.

### What is the difference between `/etc/crontab` and `/etc/cron.allow`?

`/etc/crontab` defines scheduled jobs, while `/etc/cron.allow` controls who may manage user crontabs.

---

## Key Takeaway

The challenge can be summarized as:

```text
james -> allowed
rod   -> denied
```

Implemented with:

```bash
echo "james" | sudo tee -a /etc/cron.allow
echo "rod" | sudo tee -a /etc/cron.deny
```

This is a simple but important Linux access-control mechanism for securing scheduled task management.
