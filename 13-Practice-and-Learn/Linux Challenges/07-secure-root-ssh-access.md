# Secure Root SSH Access

## Overview

SSH (Secure Shell) is one of the most common ways to remotely administer Linux servers.

Because the `root` account has unrestricted administrative privileges, allowing direct SSH login as `root` increases security risk. A common hardening practice is therefore to **disable direct root login over SSH** and require administrators to:

1. log in using a normal user account
2. elevate privileges with `sudo` when administrative access is needed

This challenge focuses on securing SSH by changing the SSH daemon configuration.

---

## Why Direct Root SSH Login Is Risky

The `root` username is universally known.

An attacker does not need to guess the username; they only need to attack the authentication mechanism.

Allowing direct root login can therefore make brute-force and credential attacks easier.

A more secure approach is:

```text
Remote User
    |
    v
Normal Linux Account
    |
    v
sudo
    |
    v
Administrative Privileges
```

This provides better accountability and reduces direct exposure of the root account.

---

## Main SSH Configuration File

The SSH server is configured primarily through:

```bash
/etc/ssh/sshd_config
```

The relevant directive is:

```text
PermitRootLogin
```

Common values include:

```text
PermitRootLogin yes
PermitRootLogin no
PermitRootLogin prohibit-password
```

For this challenge, the secure setting is typically:

```text
PermitRootLogin no
```

This completely disables direct SSH login as `root`.

---

## Step 1 — Check the Current Configuration

Before making changes, inspect the current value:

```bash
sudo grep -i '^PermitRootLogin' /etc/ssh/sshd_config
```

If nothing is returned, the directive may be:

- commented out
- inherited from a default
- defined in an included configuration file

You can also search more broadly:

```bash
sudo grep -Ri 'PermitRootLogin' /etc/ssh/
```

---

## Step 2 — Edit the SSH Configuration

Open the SSH daemon configuration:

```bash
sudo vi /etc/ssh/sshd_config
```

or:

```bash
sudo nano /etc/ssh/sshd_config
```

Locate:

```text
PermitRootLogin
```

and set:

```text
PermitRootLogin no
```

If you see:

```text
#PermitRootLogin yes
```

remove the `#` and change it to:

```text
PermitRootLogin no
```

---

## Step 3 — Validate the SSH Configuration

Before restarting SSH, validate the configuration syntax:

```bash
sudo sshd -t
```

If the command produces no output, the configuration is valid.

This is a very useful real-world habit because a syntax error in `sshd_config` can prevent SSH from starting correctly.

---

## Step 4 — Reload or Restart SSH

On RHEL, Rocky Linux, CentOS, and similar systems:

```bash
sudo systemctl restart sshd
```

A less disruptive option is often:

```bash
sudo systemctl reload sshd
```

On some Debian/Ubuntu systems, the service may be named:

```bash
sudo systemctl restart ssh
```

---

## Step 5 — Verify the Service

Check that SSH is running correctly:

```bash
sudo systemctl status sshd
```

You can also inspect the effective SSH configuration:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected result:

```text
permitrootlogin no
```

---

## Verification Test

A direct SSH attempt as root should now fail:

```bash
ssh root@server-ip
```

Instead, administrators should connect with a normal user:

```bash
ssh user@server-ip
```

and then elevate privileges:

```bash
sudo -i
```

or run individual administrative commands:

```bash
sudo systemctl status sshd
```

---

## Important Safety Practice

When modifying SSH settings on a remote machine, do **not** immediately close your current session.

A safer workflow is:

```text
1. Keep current SSH session open
2. Edit sshd_config
3. Run sshd -t
4. Reload/restart SSH
5. Open a second terminal
6. Test a new SSH connection
7. Close the original session only after success
```

This helps prevent accidental lockout.

---

## Difference Between Common `PermitRootLogin` Values

| Value | Meaning |
|---|---|
| `yes` | Root can log in using permitted authentication methods |
| `no` | Root SSH login is completely disabled |
| `prohibit-password` | Root password login is disabled, but key-based login may still work |
| `forced-commands-only` | Root key login is allowed only for forced commands |

For strict hardening, use:

```text
PermitRootLogin no
```

---

## Why Use `sudo` Instead?

Using a normal account plus `sudo` has several advantages:

- reduces direct exposure of the `root` account
- improves accountability
- allows fine-grained privilege control
- works well with audit logging
- supports role-based administration
- reduces the impact of compromised credentials

Example:

```bash
ssh admin@server
sudo dnf update
```

is preferable to:

```bash
ssh root@server
```

---

## Useful Commands

### Check SSH service

```bash
systemctl status sshd
```

### Check root-login setting

```bash
grep -i '^PermitRootLogin' /etc/ssh/sshd_config
```

### Validate SSH configuration

```bash
sshd -t
```

### Show effective SSH configuration

```bash
sshd -T | grep permitrootlogin
```

### Restart SSH daemon

```bash
sudo systemctl restart sshd
```

### Reload SSH daemon

```bash
sudo systemctl reload sshd
```

---

## Common Mistakes

### 1. Editing the Wrong File

Make sure you modify:

```bash
/etc/ssh/sshd_config
```

not:

```bash
/etc/ssh/ssh_config
```

The difference is important:

```text
ssh_config   -> SSH client configuration
sshd_config  -> SSH server configuration
```

---

### 2. Forgetting to Restart or Reload SSH

Changing the file alone is not enough.

The SSH daemon must reload the configuration.

---

### 3. Restarting Without Validating

A bad configuration can potentially break SSH access.

Always run:

```bash
sudo sshd -t
```

first.

---

### 4. Leaving Duplicate Directives

If the file contains multiple `PermitRootLogin` entries, configuration behavior may become confusing.

Search with:

```bash
grep -n 'PermitRootLogin' /etc/ssh/sshd_config
```

and make sure the intended configuration is clear.

---

## Cheat Sheet

```bash
# Check current configuration
sudo grep -i '^PermitRootLogin' /etc/ssh/sshd_config

# Edit SSH daemon configuration
sudo vi /etc/ssh/sshd_config

# Secure setting
PermitRootLogin no

# Validate syntax
sudo sshd -t

# Reload configuration
sudo systemctl reload sshd

# Verify effective configuration
sudo sshd -T | grep permitrootlogin
```

---

## Interview Questions

### What is the purpose of `PermitRootLogin`?

It controls whether the `root` account is allowed to log in remotely through SSH.

### Why is disabling direct root SSH login recommended?

Because the root username is well known and has unrestricted privileges, making it a high-value target.

### What should be used instead of direct root login?

A normal user account combined with `sudo`.

### What is the difference between `ssh_config` and `sshd_config`?

`ssh_config` configures the SSH client, while `sshd_config` configures the SSH server.

### How can you test SSH configuration syntax before restarting?

Use:

```bash
sshd -t
```

### How can you view the effective SSH daemon configuration?

Use:

```bash
sshd -T
```

---

## Final Procedure

```bash
sudo vi /etc/ssh/sshd_config
```

Set:

```text
PermitRootLogin no
```

Validate:

```bash
sudo sshd -t
```

Reload:

```bash
sudo systemctl reload sshd
```

Verify:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected output:

```text
permitrootlogin no
```

---

## Key Takeaway

Disabling direct root SSH login is a basic but important Linux hardening measure.

The preferred administrative workflow is:

```text
SSH as normal user
        |
        v
      sudo
        |
        v
Administrative task
```

This provides stronger security, better accountability, and safer remote administration.
