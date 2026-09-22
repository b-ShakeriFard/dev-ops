# Install and Disable SELinux on CentOS/RHEL

## Overview

This KodeKloud challenge focused on two tasks:

1. Ensure SELinux-related packages are installed.
2. Disable SELinux persistently.

SELinux stands for **Security-Enhanced Linux**. It provides an additional mandatory access control layer on top of normal Linux file permissions.

Even though SELinux is an important security feature in production environments, some labs or application requirements may ask for it to be disabled.

---

## What Is SELinux?

Traditional Linux permissions use:

```text
user
group
others
```

SELinux adds another policy layer that can restrict what processes are allowed to do, even when normal Unix permissions would otherwise allow access.

SELinux commonly operates in three modes:

```text
Enforcing
Permissive
Disabled
```

### Enforcing

SELinux policies are actively enforced.

```text
Access violating policy -> blocked
```

### Permissive

SELinux policy violations are logged, but access is not blocked.

```text
Access violating policy -> allowed + logged
```

### Disabled

SELinux is completely disabled.

---

## Step 1 — Install SELinux Packages

On CentOS/RHEL-style systems, the required packages may include:

```bash
sudo dnf install -y selinux-policy selinux-policy-targeted policycoreutils
```

On older systems using `yum`:

```bash
sudo yum install -y selinux-policy selinux-policy-targeted policycoreutils
```

These packages provide the SELinux policy files and administrative utilities.

---

## Step 2 — Check SELinux Status

Use:

```bash
sestatus
```

Typical output may include:

```text
SELinux status:                 enabled
Current mode:                   enforcing
Mode from config file:          enforcing
```

Another useful command is:

```bash
getenforce
```

Possible outputs:

```text
Enforcing
Permissive
Disabled
```

---

## Step 3 — Temporarily Disable Enforcement

To stop SELinux from actively enforcing policy in the current session:

```bash
sudo setenforce 0
```

Then verify:

```bash
getenforce
```

Expected:

```text
Permissive
```

Important:

> `setenforce 0` does **not** fully disable SELinux and does not survive reboot.

It only switches from Enforcing to Permissive mode.

---

## Step 4 — Disable SELinux Persistently

Edit:

```bash
sudo vi /etc/selinux/config
```

Find:

```text
SELINUX=enforcing
```

or:

```text
SELINUX=permissive
```

Change it to:

```text
SELINUX=disabled
```

A typical configuration looks like:

```text
SELINUX=disabled
SELINUXTYPE=targeted
```

Save and exit.

---

## Step 5 — Reboot

A reboot is normally required for SELinux to become fully disabled:

```bash
sudo reboot
```

After the system returns, verify:

```bash
sestatus
```

Expected:

```text
SELinux status:                 disabled
```

---

## Temporary vs Persistent Change

This distinction is important.

### Temporary

```bash
sudo setenforce 0
```

Changes:

```text
Enforcing -> Permissive
```

The change lasts until reboot.

### Persistent

Edit:

```bash
/etc/selinux/config
```

and set:

```text
SELINUX=disabled
```

Then reboot.

So:

```text
setenforce 0
      |
      v
Permissive until reboot

SELINUX=disabled
      |
      v
Disabled after reboot
```

---

## Useful Commands

### Check SELinux state

```bash
sestatus
```

### Show current enforcement mode

```bash
getenforce
```

### Set permissive mode temporarily

```bash
sudo setenforce 0
```

### Return to enforcing mode

```bash
sudo setenforce 1
```

### Edit persistent configuration

```bash
sudo vi /etc/selinux/config
```

### Install SELinux utilities

```bash
sudo dnf install -y selinux-policy selinux-policy-targeted policycoreutils
```

---

## Common Mistakes

### 1. Thinking `setenforce 0` Means Disabled

It does not.

```bash
setenforce 0
```

means:

```text
Permissive
```

not:

```text
Disabled
```

---

### 2. Forgetting to Edit `/etc/selinux/config`

If the challenge asks for a persistent change, modify:

```bash
/etc/selinux/config
```

and set:

```text
SELINUX=disabled
```

---

### 3. Forgetting the Reboot

A full transition to Disabled mode usually requires a reboot.

---

### 4. Editing the Wrong Value

Correct:

```text
SELINUX=disabled
```

Do not confuse this with:

```text
SELINUXTYPE=targeted
```

`SELINUXTYPE` controls the policy type, not whether SELinux is enabled.

---

## Why SELinux Is Usually Better Left Enabled

In real production systems, completely disabling SELinux is generally not preferred unless there is a specific requirement.

A safer troubleshooting approach is often:

```bash
sudo setenforce 0
```

which places SELinux in Permissive mode.

This allows administrators to inspect audit logs and fix policy issues instead of removing the security layer entirely.

In enterprise Linux environments, keeping SELinux enabled is usually considered a stronger security posture.

---

## Cheat Sheet

```bash
# Install SELinux components
sudo dnf install -y selinux-policy selinux-policy-targeted policycoreutils

# Check status
sestatus

# Check enforcement mode
getenforce

# Temporarily make SELinux permissive
sudo setenforce 0

# Edit persistent setting
sudo vi /etc/selinux/config
```

Set:

```text
SELINUX=disabled
```

Then:

```bash
sudo reboot
```

Verify:

```bash
sestatus
```

---

## Interview Questions

### What is SELinux?

SELinux is a mandatory access control system that adds an additional security policy layer beyond traditional Linux permissions.

### What are the three SELinux modes?

```text
Enforcing
Permissive
Disabled
```

### What does `setenforce 0` do?

It changes SELinux from Enforcing to Permissive mode for the current running system.

### Does `setenforce 0` survive reboot?

No.

### How do you disable SELinux persistently?

Set:

```text
SELINUX=disabled
```

in:

```bash
/etc/selinux/config
```

and reboot.

### What does `getenforce` show?

The current SELinux enforcement mode.

### What is the difference between Permissive and Disabled?

Permissive mode still loads SELinux policies and logs violations, while Disabled mode turns SELinux off entirely.

---

## Key Takeaway

The core workflow was:

```bash
sudo dnf install -y selinux-policy selinux-policy-targeted policycoreutils
```

then:

```bash
sudo vi /etc/selinux/config
```

set:

```text
SELINUX=disabled
```

and reboot:

```bash
sudo reboot
```

Finally verify:

```bash
sestatus
```

The most important lesson is:

```text
setenforce 0     -> temporary permissive mode
SELINUX=disabled -> persistent disable after reboot
```
