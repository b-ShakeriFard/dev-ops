# Temporary Linux User with Expiry Date

## Task

Create a temporary Linux user account that automatically expires on a specific date.

## Create the User

Example:

```bash
sudo useradd -e 2026-12-31 rose
```

The `-e` option sets the **account expiry date**.

Date format:

```text
YYYY-MM-DD
```

Example:

```text
2026-12-31
```

## Verify the Expiry Date

Use:

```bash
sudo chage -l rose
```

Look for:

```text
Account expires : Dec 31, 2026
```

You can also inspect the user entry with:

```bash
getent passwd rose
```

## Change Expiry for an Existing User

Using `chage`:

```bash
sudo chage -E 2026-12-31 rose
```

Or using `usermod`:

```bash
sudo usermod -e 2026-12-31 rose
```

## Important Distinction

Account expiry is different from password expiry.

```text
Account expiry  → disables the entire user account after a date
Password expiry → requires the user to change their password
```

## Key Command Breakdown

```text
useradd   → create a user
-e        → set account expiry date
chage -l  → display account/password aging information
chage -E  → set account expiry date
usermod -e → modify expiry date for an existing user
```

## Key Takeaway

For a temporary user:

```bash
sudo useradd -e YYYY-MM-DD username
```

Then verify:

```bash
sudo chage -l username
```
