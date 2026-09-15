# Linux Timezone Alignment with `timedatectl`

## Overview

This challenge focused on aligning a Linux server's timezone with a required regional timezone.

The required timezone was:

```text
Africa/Bamako
```

On modern Linux systems that use `systemd`, timezone configuration is typically managed with:

```bash
timedatectl
```

The essential task was:

```bash
sudo timedatectl set-timezone Africa/Bamako
```

and then verifying that the change took effect.

---

## Why Timezone Configuration Matters

Correct timezone configuration is important for:

- system logs
- scheduled jobs
- cron tasks
- application timestamps
- monitoring systems
- incident analysis
- audit trails
- distributed systems

A wrong timezone can make troubleshooting very confusing, especially when comparing logs from multiple servers.

---

## Check the Current Timezone

Use:

```bash
timedatectl
```

or:

```bash
timedatectl status
```

Example output:

```text
Local time: Mon 2026-09-15 03:10:00 GMT
Universal time: Mon 2026-09-15 03:10:00 UTC
RTC time: Mon 2026-09-15 03:10:00
Time zone: Etc/UTC (UTC, +0000)
```

The important line is:

```text
Time zone:
```

---

## List Available Timezones

To see all supported timezones:

```bash
timedatectl list-timezones
```

Because the list is long, filtering is useful.

For Bamako:

```bash
timedatectl list-timezones | grep Africa/Bamako
```

Expected:

```text
Africa/Bamako
```

This confirms that the timezone exists on the system.

---

## Set the Required Timezone

Use:

```bash
sudo timedatectl set-timezone Africa/Bamako
```

This updates the system timezone configuration.

---

## Verify the Change

Run:

```bash
timedatectl
```

or:

```bash
timedatectl | grep "Time zone"
```

Expected output:

```text
Time zone: Africa/Bamako
```

---

## Full Procedure

```bash
timedatectl

timedatectl list-timezones | grep Africa/Bamako

sudo timedatectl set-timezone Africa/Bamako

timedatectl | grep "Time zone"
```

---

## What Actually Changes?

Changing the timezone does not normally change the underlying UTC system clock.

Instead, Linux changes how local time is interpreted and displayed.

Conceptually:

```text
System clock (UTC)
        |
        v
Timezone setting
        |
        v
Local displayed time
```

This means the same moment in time can be displayed differently depending on the configured timezone.

---

## About `Africa/Bamako`

`Africa/Bamako` corresponds to Bamako, Mali.

This timezone uses:

```text
UTC+00:00
```

and does not currently use daylight saving time.

That means local time in Bamako is aligned with UTC.

---

## How Linux Stores the Timezone

On many modern Linux distributions, the active timezone is represented through:

```bash
/etc/localtime
```

This is usually a symbolic link to a timezone file under:

```bash
/usr/share/zoneinfo/
```

For example:

```text
/etc/localtime
   |
   v
/usr/share/zoneinfo/Africa/Bamako
```

You can inspect it with:

```bash
ls -l /etc/localtime
```

However, using `timedatectl` is generally cleaner and safer than modifying the symlink manually.

---

## Common Mistakes

### 1. Typing the Timezone Incorrectly

Wrong:

```text
Africa/bamako
```

Correct:

```text
Africa/Bamako
```

Timezone names are case-sensitive.

---

### 2. Forgetting `sudo`

Changing the system timezone requires administrative privileges.

Use:

```bash
sudo timedatectl set-timezone Africa/Bamako
```

---

### 3. Assuming the Clock Itself Is Wrong

A timezone mismatch does not necessarily mean the system clock is incorrect.

The underlying UTC clock may be perfectly correct while the local timezone setting is wrong.

---

### 4. Not Verifying

Always confirm the result:

```bash
timedatectl
```

or:

```bash
timedatectl | grep "Time zone"
```

---

## Useful Commands

### Show current date and time

```bash
date
```

### Show timezone information

```bash
timedatectl
```

### List available timezones

```bash
timedatectl list-timezones
```

### Search for a timezone

```bash
timedatectl list-timezones | grep Bamako
```

### Set timezone

```bash
sudo timedatectl set-timezone Africa/Bamako
```

### Check `/etc/localtime`

```bash
ls -l /etc/localtime
```

---

## Cheat Sheet

```bash
# Check current timezone
timedatectl

# Search for Bamako timezone
timedatectl list-timezones | grep Africa/Bamako

# Set timezone
sudo timedatectl set-timezone Africa/Bamako

# Verify
timedatectl | grep "Time zone"
```

Expected:

```text
Time zone: Africa/Bamako
```

---

## Interview Questions

### What does `timedatectl` do?

It manages and displays system time, timezone, and related systemd time settings.

### How do you change the timezone?

Use:

```bash
sudo timedatectl set-timezone REGION/CITY
```

### How do you list supported timezones?

Use:

```bash
timedatectl list-timezones
```

### Does changing the timezone change the UTC system clock?

Usually no. It changes how local time is displayed and interpreted.

### Where are timezone files stored?

Typically under:

```bash
/usr/share/zoneinfo/
```

### What file represents the active timezone?

Usually:

```bash
/etc/localtime
```

---

## Key Takeaway

The essential command for this challenge was:

```bash
sudo timedatectl set-timezone Africa/Bamako
```

Verification:

```bash
timedatectl | grep "Time zone"
```

Expected result:

```text
Time zone: Africa/Bamako
```

Timezone alignment is a small configuration task, but it is important for reliable logs, scheduling, monitoring, and cross-system troubleshooting.
