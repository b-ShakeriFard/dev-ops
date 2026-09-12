# Linux File Permission Correction with ACLs

## Overview

This challenge focused on correcting permissions for a critical Linux file using both traditional Linux permission bits and Access Control Lists (ACLs).

Target file:

```bash
/etc/hosts
```

Required state:

1. Owner must be `root`
2. Group owner must be `root`
3. `others` must have read-only permission
4. User `james` must have no permissions
5. User `garrett` must have read-only permission

This is a great example of when normal Unix permission bits are not enough and ACLs are needed.

---

## Why ACLs Are Needed

Traditional Linux permissions provide only three classes:

```text
owner
group
others
```

For example:

```text
-rw-r--r--
```

means:

```text
owner  = read + write
group  = read
others = read
```

But this challenge requires special rules for two named users:

```text
james   -> no access
garrett -> read-only
```

That cannot be expressed with only owner/group/others. ACLs solve this by allowing per-user and per-group exceptions.

---

## Step 1 — Inspect Current Permissions

```bash
ls -l /etc/hosts
```

Example:

```text
-rw-r--r-- 1 root root 284 Sep 12 12:41 /etc/hosts
```

This already shows:

```text
owner  = root
group  = root
others = read-only
```

---

## Step 2 — Ensure Ownership Is Correct

```bash
sudo chown root:root /etc/hosts
```

Verify:

```bash
ls -l /etc/hosts
```

---

## Step 3 — Ensure Standard Permissions Are Correct

The standard mode should be:

```text
rw-r--r--
```

Numerically:

```text
644
```

Apply it with:

```bash
sudo chmod 644 /etc/hosts
```

Meaning:

```text
6 = rw-
4 = r--
4 = r--
```

So:

```text
owner  -> read + write
group  -> read
others -> read
```

---

## Step 4 — Deny All Permissions to `james`

Use:

```bash
sudo setfacl -m u:james:--- /etc/hosts
```

Breakdown:

```text
setfacl     -> modify ACLs
-m          -> modify/add an ACL entry
u:james     -> named user james
---         -> no read, no write, no execute
```

Important distinction:

```bash
setfacl -x u:james /etc/hosts
```

does **not** mean “deny James.”

It removes James's special ACL entry. If that happens, James falls back to the normal group/other permissions, which may still allow access.

Therefore this is the correct denial rule:

```bash
setfacl -m u:james:--- /etc/hosts
```

---

## Step 5 — Grant Read-Only Permission to `garrett`

```bash
sudo setfacl -m u:garrett:r-- /etc/hosts
```

This gives Garrett read permission only.

---

## Step 6 — Verify the ACL

```bash
getfacl /etc/hosts
```

Expected result:

```text
# file: etc/hosts
# owner: root
# group: root
user::rw-
user:james:---
user:garrett:r--
group::r--
mask::r--
other::r--
```

This satisfies all requirements.

---

## Understanding the ACL Output

### Owner

```text
user::rw-
```

The file owner has read and write permission.

### James

```text
user:james:---
```

James has no permissions.

### Garrett

```text
user:garrett:r--
```

Garrett has read-only permission.

### Group

```text
group::r--
```

The owning group has read-only permission.

### Mask

```text
mask::r--
```

The ACL mask limits the maximum effective permission for named users, named groups, and the owning group.

### Others

```text
other::r--
```

All unmatched users receive read-only access.

---

## Final Commands

```bash
sudo chown root:root /etc/hosts
sudo chmod 644 /etc/hosts
sudo setfacl -m u:james:--- /etc/hosts
sudo setfacl -m u:garrett:r-- /etc/hosts
getfacl /etc/hosts
```

---

## Common Mistakes

### 1. Removing an ACL Instead of Denying Access

Wrong:

```bash
sudo setfacl -x u:james /etc/hosts
```

This only removes James's custom ACL rule.

Correct:

```bash
sudo setfacl -m u:james:--- /etc/hosts
```

This explicitly gives James no permissions.

---

### 2. Using Invalid ACL Syntax

Correct named-user syntax:

```text
u:username:permissions
```

Example:

```bash
u:garrett:r--
```

---

### 3. Forgetting to Verify

Always check the resulting ACL:

```bash
getfacl /etc/hosts
```

`ls -l` alone does not show all per-user ACL details.

---

## Useful Commands

### Show normal permissions

```bash
ls -l /etc/hosts
```

### Show ACLs

```bash
getfacl /etc/hosts
```

### Add or modify a named-user ACL

```bash
setfacl -m u:username:r-- file
```

### Explicitly deny a named user

```bash
setfacl -m u:username:--- file
```

### Remove one ACL entry

```bash
setfacl -x u:username file
```

### Remove all extended ACL entries

```bash
setfacl -b file
```

Use `-b` carefully because it removes all extended ACL rules.

---

## ACL vs Standard Permissions

Traditional permissions:

```text
owner / group / others
```

ACLs add:

```text
specific users
specific groups
fine-grained exceptions
```

Conceptually:

```text
Standard permissions
        |
        v
owner / group / others
        |
        v
ACL exceptions
        |
        +--> james   -> ---
        |
        +--> garrett -> r--
```

---

## Cheat Sheet

```bash
# Check ownership and mode
ls -l /etc/hosts

# Set ownership
sudo chown root:root /etc/hosts

# Set rw-r--r--
sudo chmod 644 /etc/hosts

# Explicitly deny james
sudo setfacl -m u:james:--- /etc/hosts

# Give garrett read-only
sudo setfacl -m u:garrett:r-- /etc/hosts

# Verify ACL
getfacl /etc/hosts
```

---

## Interview Questions

### What is an ACL in Linux?

An Access Control List allows more fine-grained permissions for specific users and groups beyond the standard owner/group/others model.

### Why was ACL required in this task?

Because specific named users needed permissions different from the general `others` permission.

### What does `setfacl -m` do?

It creates or modifies an ACL entry.

### What does `setfacl -x` do?

It removes a specific ACL entry.

### What is the difference between these commands?

```bash
setfacl -x u:james file
```

removes James's custom ACL.

```bash
setfacl -m u:james:--- file
```

explicitly gives James no permissions.

### What does the ACL mask do?

It defines the maximum effective permissions available to named users, named groups, and the owning group.

### Why is `getfacl` important?

It displays the full ACL configuration, which cannot be completely seen with `ls -l`.

---

## Key Takeaway

The biggest lesson from this challenge is the difference between:

```text
removing a permission rule
```

and:

```text
explicitly denying permissions
```

The final desired policy was:

```text
root      -> rw-
group     -> r--
others    -> r--
james     -> ---
garrett   -> r--
```

ACLs make this kind of fine-grained access control possible while preserving the normal Linux permission model.
