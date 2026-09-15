# Temporary User Setup with Expiration

## Overview

This Linux challenge focuses on creating a **temporary user account** that automatically expires on a specific date.

This is useful when creating accounts for:

- contractors
- temporary staff
- interns
- short-term project access
- maintenance windows
- time-limited lab users

Instead of manually deleting or disabling the account later, Linux can enforce an account expiration date automatically.

---

## Core Command

The key option is:

```bash
-e
```

with `useradd`.

Example:

```bash
sudo useradd -e 2026-09-30 tempuser
```

This creates the user:

```text
tempuser
```

and sets the account expiration date to:

```text
2026-09-30
```

---

## Command Breakdown

```bash
sudo useradd -e 2026-09-30 tempuser
```

| Part | Meaning |
|---|---|
| `sudo` | Run with administrative privileges |
| `useradd` | Create a new user |
| `-e` | Set account expiration date |
| `2026-09-30` | Expiration date in `YYYY-MM-DD` format |
| `tempuser` | Username |

---

## Verify the Account

Use:

```bash
sudo chage -l tempuser
```

Look for:

```text
Account expires : Sep 30, 2026
```

This confirms the expiration date.

You can also inspect the user account with:

```bash
getent passwd tempuser
```

---

## `useradd -e` vs Password Expiration

This is an important distinction.

### Account expiration

```bash
useradd -e DATE username
```

This expires the **entire user account**.

After the expiration date, the account should no longer be usable for normal login.

### Password expiration

Password aging is different and is managed with commands such as:

```bash
chage
```

For example:

```bash
sudo chage -M 90 username
```

means the password must be changed after 90 days.

So:

```text
Account expiration  -> disables account after a date
Password expiration -> forces password change after a period
```

---

## Create User with an Expiration Date

Example:

```bash
sudo useradd -e 2026-12-31 contractor1
```

Verify:

```bash
sudo chage -l contractor1
```

---

## Change Expiration Date for an Existing User

If the user already exists:

```bash
sudo chage -E 2026-12-31 contractor1
```

or:

```bash
sudo usermod -e 2026-12-31 contractor1
```

Both can update the account expiration date.

---

## Remove the Expiration Date

To remove an expiration date with `usermod`:

```bash
sudo usermod -e "" contractor1
```

Or with `chage`:

```bash
sudo chage -E -1 contractor1
```

This restores the account to no fixed expiration date.

---

## Date Format

Use:

```text
YYYY-MM-DD
```

Example:

```text
2026-09-30
```

Avoid ambiguous formats such as:

```text
09/30/26
30-09-2026
```

The ISO format is clearer and works well in Linux tools.

---

## Useful Commands

### Create expiring user

```bash
sudo useradd -e 2026-09-30 tempuser
```

### Verify expiration

```bash
sudo chage -l tempuser
```

### Change expiration date

```bash
sudo usermod -e 2026-10-31 tempuser
```

### Alternative with `chage`

```bash
sudo chage -E 2026-10-31 tempuser
```

### Remove expiration

```bash
sudo chage -E -1 tempuser
```

---

## Common Mistakes

### 1. Confusing Account Expiry with Password Expiry

This:

```bash
useradd -e 2026-09-30 tempuser
```

expires the **account**, not just the password.

---

### 2. Using the Wrong Date Format

Correct:

```text
2026-09-30
```

Avoid locale-dependent date formats.

---

### 3. Forgetting to Verify

Always check:

```bash
sudo chage -l tempuser
```

Do not assume the account was created with the expected expiration date.

---

### 4. Setting the Date in the Past

If the expiration date is already past, the account may immediately become unusable.

Always verify the system date if needed:

```bash
date
```

---

## Practical Example

Suppose a contractor should have access only until October 15, 2026.

Create the user:

```bash
sudo useradd -e 2026-10-15 contractor
```

Set a password:

```bash
sudo passwd contractor
```

Verify:

```bash
sudo chage -l contractor
```

Expected:

```text
Account expires : Oct 15, 2026
```

---

## Cheat Sheet

```bash
# Create temporary user
sudo useradd -e YYYY-MM-DD username

# Example
sudo useradd -e 2026-09-30 tempuser

# Verify
sudo chage -l tempuser

# Change expiry
sudo usermod -e 2026-10-31 tempuser

# Alternative
sudo chage -E 2026-10-31 tempuser

# Remove expiry
sudo chage -E -1 tempuser
```

---

## Interview Questions

### What does `useradd -e` do?

It sets the account expiration date for a new Linux user.

### What date format should be used?

`YYYY-MM-DD`.

### How do you verify the expiration date?

Use:

```bash
chage -l username
```

### What is the difference between account expiry and password expiry?

Account expiry disables the whole account, while password expiry only requires the user to change their password.

### How do you change the expiration date of an existing account?

Use:

```bash
usermod -e DATE username
```

or:

```bash
chage -E DATE username
```

### How do you remove account expiration?

Use:

```bash
chage -E -1 username
```

---

## Key Takeaway

The essential pattern is:

```bash
sudo useradd -e YYYY-MM-DD username
```

For example:

```bash
sudo useradd -e 2026-09-30 tempuser
```

Then verify with:

```bash
sudo chage -l tempuser
```

This is a simple and effective way to enforce **time-limited user access** on Linux systems.
