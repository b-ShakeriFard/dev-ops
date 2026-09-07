# Linux User with Non-Interactive Shell

## Task

Create a user named `rose` on a Linux server and assign a non-interactive shell.

## Command

```bash
sudo useradd -s /usr/sbin/nologin rose
```

## Verify

```bash
getent passwd rose
```

Expected ending:

```text
/usr/sbin/nologin
```

Example:

```text
rose:x:1001:1001::/home/rose:/usr/sbin/nologin
```

## What Does `nologin` Mean?

A user with:

```text
/usr/sbin/nologin
```

cannot open a normal interactive shell session.

The account can still be useful for:

- running services
- owning files
- application/service identities
- automated processes

## Key Command Breakdown

```text
useradd   → create a user
-s        → specify the login shell
nologin   → prevent interactive login
```

## Key Takeaway

```bash
sudo useradd -s /usr/sbin/nologin rose
```

creates a normal Linux account that is not intended for interactive user login.
