# Secure Root SSH Access

## Task

Harden SSH access by preventing direct login to the server as the `root` user.

Instead of connecting directly as:

```bash
ssh root@server
```

administrators should log in using a normal user account and elevate privileges with `sudo` when required.

---

## Why Disable Direct Root SSH Login?

The `root` account has unrestricted control over the system.

Allowing direct SSH access to `root` increases risk because:

- attackers know the root username always exists;
- successful compromise immediately gives full administrative access;
- using named user accounts provides better auditing;
- `sudo` allows controlled privilege escalation.

A safer model is:

```text
Normal User
   |
   | SSH
   v
Linux Server
   |
   | sudo
   v
Administrative Privileges
```

---

## SSH Configuration File

The SSH server configuration is usually stored in:

```text
/etc/ssh/sshd_config
```

Edit it with:

```bash
sudo vi /etc/ssh/sshd_config
```

Find:

```text
PermitRootLogin
```

and set:

```text
PermitRootLogin no
```

If the line is commented, for example:

```text
#PermitRootLogin yes
```

replace it with:

```text
PermitRootLogin no
```

---

## Validate the Configuration

Before restarting SSH, check the configuration syntax:

```bash
sudo sshd -t
```

If no output is returned, the configuration is syntactically valid.

---

## Reload SSH

Apply the change:

```bash
sudo systemctl reload sshd
```

You can also restart the service if required:

```bash
sudo systemctl restart sshd
```

---

## Verify the Effective Setting

Run:

```bash
sudo sshd -T | grep permitrootlogin
```

Expected:

```text
permitrootlogin no
```

You can also inspect the configured file:

```bash
grep -i '^PermitRootLogin' /etc/ssh/sshd_config
```

---

## Test

From another machine:

```bash
ssh root@server
```

Direct root login should be rejected.

A normal administrative user should still be able to connect:

```bash
ssh adminuser@server
```

and elevate privileges:

```bash
sudo -i
```

---

## Important Safety Check

Before disabling root SSH login, make sure you already have:

- a normal user account;
- working SSH access for that user;
- working `sudo` privileges.

Otherwise, you may lock yourself out of the server.

---

## Related `PermitRootLogin` Values

```text
yes                  → root SSH login allowed
no                   → root SSH login completely disabled
prohibit-password    → root may use SSH keys, but not passwords
forced-commands-only → root key login allowed only for forced commands
```

For a task that specifically asks to restrict direct root SSH access, the usual setting is:

```text
PermitRootLogin no
```

---

## Key Takeaway

The core hardening step is:

```text
PermitRootLogin no
```

followed by validation and an SSH reload:

```bash
sudo sshd -t
sudo systemctl reload sshd
```

This forces administrators to log in using named accounts and use `sudo` for privileged operations.
