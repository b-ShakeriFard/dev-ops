# Linux Cron Job for Root User

## Overview

This KodeKloud challenge required configuring a cron job for the **root user** on all application servers.

The job had to run every 5 minutes and write:

```text
hello
```

to:

```bash
/tmp/cron_text
```

The final cron entry was:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

## Install Cron on CentOS/RHEL

On CentOS/RHEL-style systems, install:

```bash
sudo dnf install -y cronie
```

Remember:

```text
cronie   -> package
crond    -> daemon/service
crontab  -> command
```

Then enable and start the service:

```bash
sudo systemctl enable --now crond
```

Verify:

```bash
systemctl is-active crond
```

Expected:

```text
active
```

## Why "Root User" Matters

Each user has a separate crontab.

This:

```bash
crontab -e
```

edits the current user's crontab.

But the task required the cron job for root, so use:

```bash
sudo crontab -e
```

Verify root's crontab with:

```bash
sudo crontab -l
```

A job in root's crontab runs with root privileges.

## Cron Schedule

Cron uses:

```text
* * * * * command
| | | | |
| | | | +-- day of week
| | | +---- month
| | +------ day of month
| +-------- hour
+---------- minute
```

For every 5 minutes:

```text
*/5 * * * *
```

So:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

means:

> Every 5 minutes, overwrite `/tmp/cron_text` with `hello`.

## `>` vs `>>`

```bash
>
```

overwrites the file.

```bash
>>
```

appends to the file.

For this task:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

was the required form.

## Configure Each App Server

Repeat on:

```text
stapp01
stapp02
stapp03
```

Commands:

```bash
sudo dnf install -y cronie
sudo systemctl enable --now crond
sudo crontab -e
```

Add:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

Then verify:

```bash
sudo crontab -l
```

## Verification

Check the cron entry:

```bash
sudo crontab -l
```

Check the daemon:

```bash
systemctl is-active crond
```

After the next 5-minute interval:

```bash
cat /tmp/cron_text
```

Expected:

```text
hello
```

## Common Mistakes

### Wrong package name

Wrong:

```bash
sudo dnf install crony
```

Correct:

```bash
sudo dnf install cronie
```

### Wrong user's crontab

Wrong for this task:

```bash
crontab -e
```

Correct:

```bash
sudo crontab -e
```

### Typo in the command

Wrong:

```cron
*/5 * * * * ech "hello" > /tmp/cron_text
```

Correct:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

### Forgetting one application server

If the task applies to all app servers, configure all three:

```text
stapp01
stapp02
stapp03
```

## Cheat Sheet

```bash
sudo dnf install -y cronie
sudo systemctl enable --now crond
sudo crontab -e
```

Add:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

Verify:

```bash
sudo crontab -l
systemctl is-active crond
```

## Interview Questions

### What is `crond`?

The daemon that executes scheduled cron jobs.

### What is `cronie`?

The package providing cron functionality on many CentOS/RHEL systems.

### What does `*/5` mean?

Every 5 minutes.

### Why use `sudo crontab -e`?

Because the task requires the cron job to belong to root.

### Does a cron job run with the privileges of its owner?

Yes. A root crontab job runs with root privileges.

## Key Takeaway

The working configuration on each app server was:

```bash
sudo dnf install -y cronie
sudo systemctl enable --now crond
sudo crontab -e
```

with:

```cron
*/5 * * * * echo "hello" > /tmp/cron_text
```

The key idea is:

```text
correct schedule + correct user + running crond = working cron job
```
