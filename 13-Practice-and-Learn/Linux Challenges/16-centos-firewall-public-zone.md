# CentOS Firewall Configuration — Set Zone to `public`

## Overview

This KodeKloud challenge focused on configuring the Linux firewall on a **CentOS** system and ensuring that the firewall zone was set to:

```text
public
```

On CentOS, the standard firewall management service is usually:

```text
firewalld
```

and the main administrative command is:

```bash
firewall-cmd
```

This challenge was slightly interesting because the system was very minimal:

- `ip` was not installed
- `nmcli` was not installed

So we had to use a lower-level method to discover the network interface.

---

## What Is `firewalld`?

`firewalld` is a dynamic firewall management service used by many RHEL-family distributions, including:

- CentOS
- Rocky Linux
- AlmaLinux
- RHEL

It organizes firewall rules into **zones**.

A zone represents a level of trust and a set of firewall rules for network traffic.

---

## Common Firewall Zones

Some common `firewalld` zones are:

```text
public
home
work
trusted
internal
external
dmz
block
drop
```

The `public` zone is intended for systems connected to networks where other machines are not fully trusted.

It is a common default for general-purpose servers.

---

## Step 1 — Check Firewall Status

First verify that `firewalld` is running:

```bash
sudo systemctl status firewalld
```

You can also check directly with:

```bash
sudo firewall-cmd --state
```

Expected output:

```text
running
```

---

## Step 2 — Check the Default Zone

Use:

```bash
sudo firewall-cmd --get-default-zone
```

If the output is:

```text
public
```

then the default zone is already correct.

If not, set it with:

```bash
sudo firewall-cmd --set-default-zone=public
```

---

## Step 3 — Check Active Zones

Use:

```bash
sudo firewall-cmd --get-active-zones
```

Example:

```text
public
  interfaces: eth0
```

This shows which network interface is currently associated with which firewall zone.

---

## Step 4 — Discover the Network Interface

Normally we might use:

```bash
ip addr
```

or:

```bash
nmcli device status
```

However, on this minimal CentOS system both commands were unavailable.

The reliable fallback was:

```bash
ls /sys/class/net
```

Example output:

```text
eth0
lo
```

or:

```text
ens3
lo
```

The `lo` interface is the loopback interface.

The other interface, such as `eth0` or `ens3`, is typically the main network interface.

---

## Step 5 — Assign the Interface to the `public` Zone

If the interface is `eth0`, use:

```bash
sudo firewall-cmd --permanent --zone=public --change-interface=eth0
```

Then reload the firewall:

```bash
sudo firewall-cmd --reload
```

If the interface is named differently, replace `eth0` accordingly.

Example:

```bash
sudo firewall-cmd --permanent --zone=public --change-interface=ens3
sudo firewall-cmd --reload
```

---

## Step 6 — Verify the Configuration

Check active zones again:

```bash
sudo firewall-cmd --get-active-zones
```

Expected result:

```text
public
  interfaces: eth0
```

Also check the default zone:

```bash
sudo firewall-cmd --get-default-zone
```

Expected:

```text
public
```

---

## Final Commands

A typical successful workflow is:

```bash
sudo firewall-cmd --get-default-zone

ls /sys/class/net

sudo firewall-cmd --set-default-zone=public

sudo firewall-cmd --permanent --zone=public --change-interface=eth0

sudo firewall-cmd --reload

sudo firewall-cmd --get-active-zones
```

Replace `eth0` with the actual interface name on the system.

---

## Why `/sys/class/net` Works

Linux exposes network interfaces through the virtual filesystem:

```bash
/sys/class/net
```

Listing this directory shows available interfaces even if user-space networking tools such as:

```text
ip
nmcli
ifconfig
```

are missing.

This makes it a useful troubleshooting fallback on minimal systems.

---

## Runtime vs Permanent Configuration

This is an important `firewalld` concept.

A command without:

```bash
--permanent
```

changes only the **runtime** configuration.

Example:

```bash
sudo firewall-cmd --zone=public --change-interface=eth0
```

This works immediately but may not survive a reboot or reload.

For persistent configuration:

```bash
sudo firewall-cmd --permanent --zone=public --change-interface=eth0
```

Then apply it:

```bash
sudo firewall-cmd --reload
```

---

## Useful Commands

### Check firewall state

```bash
sudo firewall-cmd --state
```

### Show default zone

```bash
sudo firewall-cmd --get-default-zone
```

### Set default zone

```bash
sudo firewall-cmd --set-default-zone=public
```

### Show active zones

```bash
sudo firewall-cmd --get-active-zones
```

### List all rules for the public zone

```bash
sudo firewall-cmd --zone=public --list-all
```

### Assign an interface to the public zone

```bash
sudo firewall-cmd --permanent --zone=public --change-interface=eth0
```

### Reload firewall rules

```bash
sudo firewall-cmd --reload
```

---

## Common Mistakes

### 1. Assuming `ip` Is Always Installed

On minimal CentOS installations:

```bash
ip addr
```

may return:

```text
-bash: ip: command not found
```

Fallback:

```bash
ls /sys/class/net
```

---

### 2. Assuming `nmcli` Is Available

Minimal images may not include NetworkManager CLI tools.

Again:

```bash
ls /sys/class/net
```

is a simple fallback.

---

### 3. Forgetting `--permanent`

Without:

```bash
--permanent
```

the change may only exist at runtime.

---

### 4. Forgetting to Reload

After changing permanent rules:

```bash
sudo firewall-cmd --reload
```

must be run to apply them to the runtime configuration.

---

### 5. Confusing Default Zone with Interface Zone

These are related but not identical.

The default zone is checked with:

```bash
firewall-cmd --get-default-zone
```

An interface's active zone is checked with:

```bash
firewall-cmd --get-active-zones
```

For a challenge that says:

> Ensure the zone is set to public

it is safest to verify both.

---

## Cheat Sheet

```bash
# Firewall status
sudo firewall-cmd --state

# Default zone
sudo firewall-cmd --get-default-zone

# Set default zone
sudo firewall-cmd --set-default-zone=public

# Discover interfaces
ls /sys/class/net

# Assign interface
sudo firewall-cmd --permanent --zone=public --change-interface=eth0

# Apply permanent rules
sudo firewall-cmd --reload

# Verify
sudo firewall-cmd --get-active-zones
```

---

## Interview Questions

### What is `firewalld`?

A dynamic firewall management service commonly used on RHEL-family Linux systems.

### What is a firewall zone?

A zone is a predefined trust level and set of firewall rules applied to interfaces or traffic sources.

### What does the `public` zone represent?

A general-purpose zone intended for networks where other systems are not fully trusted.

### What does `--permanent` do?

It makes the firewall configuration persistent across reloads and reboots.

### Why is `firewall-cmd --reload` needed?

It loads permanent configuration changes into the active runtime firewall.

### How can you find network interfaces if `ip` and `nmcli` are missing?

Use:

```bash
ls /sys/class/net
```

### What is the difference between the default zone and an active zone?

The default zone is used when no explicit zone is assigned, while active zones show which zones are currently bound to interfaces or sources.

---

## Key Takeaway

The main challenge was not just setting the firewall zone, but doing so on a **minimal CentOS system** without normal networking tools.

The core pattern was:

```bash
ls /sys/class/net

sudo firewall-cmd --set-default-zone=public

sudo firewall-cmd --permanent --zone=public --change-interface=eth0

sudo firewall-cmd --reload

sudo firewall-cmd --get-active-zones
```

This is a useful real-world troubleshooting pattern for working with `firewalld` on stripped-down Linux servers.
