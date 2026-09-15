# Default GUI Boot Configuration with systemd

## Overview

This Linux challenge focuses on configuring a system so that it boots into the **graphical user interface (GUI)** by default instead of stopping at a text-only console.

On modern Linux distributions that use `systemd`, boot modes are controlled through **targets**.

The two most important targets for this task are:

```text
multi-user.target
graphical.target
```

A system configured with:

```text
multi-user.target
```

normally boots into a non-graphical multi-user environment.

A system configured with:

```text
graphical.target
```

boots into a graphical environment, assuming the necessary desktop packages and display manager are installed.

---

## Check the Current Default Boot Target

Use:

```bash
systemctl get-default
```

Example output:

```text
multi-user.target
```

This means the system currently boots into text mode.

If the output is:

```text
graphical.target
```

then GUI boot is already configured.

---

## Set GUI Mode as the Default

Use:

```bash
sudo systemctl set-default graphical.target
```

This changes the system's default boot target.

Verify:

```bash
systemctl get-default
```

Expected output:

```text
graphical.target
```

---

## Important Difference: `set-default` vs `isolate`

These commands are related, but they do different things.

### Change future boot behavior

```bash
sudo systemctl set-default graphical.target
```

This changes what target the system boots into after future restarts.

### Switch to GUI mode immediately

```bash
sudo systemctl isolate graphical.target
```

This attempts to move the currently running system into the graphical target immediately.

So:

```text
set-default -> future boots
isolate     -> current session
```

For a KodeKloud task asking for the **default boot configuration**, `set-default` is the important command.

---

## How systemd Implements the Default Target

The default target is represented by a symbolic link:

```bash
/etc/systemd/system/default.target
```

You can inspect it with:

```bash
ls -l /etc/systemd/system/default.target
```

When GUI mode is configured, it normally points to:

```text
/usr/lib/systemd/system/graphical.target
```

Conceptually:

```text
/etc/systemd/system/default.target
              |
              v
        graphical.target
```

The `systemctl set-default` command manages this symlink for you.

---

## What Is `graphical.target`?

`graphical.target` is a systemd target intended for systems that need a graphical login environment.

It generally includes everything required by:

```text
multi-user.target
```

plus graphical components such as a display manager.

Examples of display managers include:

```text
GDM
SDDM
LightDM
```

The exact desktop environment is distribution-dependent.

---

## What Is `multi-user.target`?

`multi-user.target` is similar to the traditional multi-user text-mode runlevel.

It usually provides:

- networking
- background services
- multiple user logins
- SSH access
- text consoles

but does not require a GUI.

This is commonly used on servers.

---

## Traditional Runlevels vs systemd Targets

Older Linux systems used SysV runlevels.

A rough comparison is:

| Traditional Runlevel | systemd Target |
|---|---|
| 3 | `multi-user.target` |
| 5 | `graphical.target` |

Modern systems use targets instead of numeric runlevels.

---

## Full Procedure

### 1. Check current target

```bash
systemctl get-default
```

### 2. Set graphical target

```bash
sudo systemctl set-default graphical.target
```

### 3. Verify

```bash
systemctl get-default
```

Expected:

```text
graphical.target
```

### 4. Optional: inspect symlink

```bash
ls -l /etc/systemd/system/default.target
```

---

## Optional Immediate Switch

If you also want to move into graphical mode without rebooting:

```bash
sudo systemctl isolate graphical.target
```

This is optional and is not required merely to configure the default boot target.

---

## Reverting to Text Mode

To boot into text mode by default:

```bash
sudo systemctl set-default multi-user.target
```

Verify:

```bash
systemctl get-default
```

Expected:

```text
multi-user.target
```

---

## Common Mistakes

### 1. Using `isolate` Instead of `set-default`

This:

```bash
sudo systemctl isolate graphical.target
```

changes the current target but does not necessarily change the next boot.

For persistent configuration, use:

```bash
sudo systemctl set-default graphical.target
```

---

### 2. Assuming `graphical.target` Installs a Desktop

It does not.

If no GUI packages or display manager are installed, setting:

```text
graphical.target
```

does not magically install them.

It only tells systemd which target should be reached by default.

---

### 3. Forgetting to Verify

Always check:

```bash
systemctl get-default
```

after changing the target.

---

## Useful Commands

```bash
# Show default boot target
systemctl get-default

# Set GUI boot
sudo systemctl set-default graphical.target

# Set text-mode boot
sudo systemctl set-default multi-user.target

# Switch to GUI immediately
sudo systemctl isolate graphical.target

# Switch to text mode immediately
sudo systemctl isolate multi-user.target

# Inspect default.target symlink
ls -l /etc/systemd/system/default.target
```

---

## Cheat Sheet

```bash
# Check current boot target
systemctl get-default

# Configure GUI boot
sudo systemctl set-default graphical.target

# Verify
systemctl get-default
```

Expected:

```text
graphical.target
```

---

## Interview Questions

### What does `systemctl get-default` do?

It displays the systemd target used by default during boot.

### What does `systemctl set-default graphical.target` do?

It configures the system to boot into the graphical target by default.

### What is the difference between `graphical.target` and `multi-user.target`?

`multi-user.target` provides a text-based multi-user environment, while `graphical.target` adds graphical login capability on top of the multi-user environment.

### What is the difference between `set-default` and `isolate`?

`set-default` affects future boots, while `isolate` changes the current running target.

### Does setting `graphical.target` install a desktop environment?

No. The required GUI packages and display manager must already be installed.

### What file represents the systemd default target?

```bash
/etc/systemd/system/default.target
```

It is normally a symbolic link to the selected target.

---

## Key Takeaway

The essential command for configuring GUI boot is:

```bash
sudo systemctl set-default graphical.target
```

Then verify with:

```bash
systemctl get-default
```

Expected result:

```text
graphical.target
```

This is the modern systemd equivalent of configuring a Linux machine to boot into a graphical runlevel by default.
