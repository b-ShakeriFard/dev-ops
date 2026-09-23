# Configure Passwordless Sudo for a Linux User

## Challenge

Grant the user `kirsty` permission to execute all administrative commands with `sudo` without entering a password.

The configuration should be created safely, validated for syntax errors, and tested without relying on an interactive password prompt.

## Step-by-Step Guide

### 1. Confirm the user exists

```bash
id kirsty
```

This should display Kirsty's user ID, primary group, and supplementary groups. If the user does not exist, stop and recheck the challenge requirements.

### 2. Create a dedicated sudoers file

Use `visudo` rather than editing sudoers files directly:

```bash
sudo visudo -f /etc/sudoers.d/kirsty
```

Add this line:

```sudoers
kirsty ALL=(ALL) NOPASSWD: ALL
```

Save and exit. With `vi`, press `Esc`, enter `:wq`, and press Enter.

The rule means:

- `kirsty` — the user receiving the privilege.
- First `ALL` — the rule applies on every host.
- `(ALL)` — Kirsty may run commands as any target user.
- `NOPASSWD:` — sudo must not request Kirsty's password.
- Final `ALL` — every command is permitted.

### 3. Set secure permissions

```bash
sudo chmod 440 /etc/sudoers.d/kirsty
```

The file is readable by root and its group but cannot be modified through ordinary file permissions.

### 4. Validate the sudoers configuration

```bash
sudo visudo -c
```

Expected output should report that `/etc/sudoers` and the included file parse correctly. Do not proceed if a syntax error is reported.

### 5. Inspect Kirsty's privileges

```bash
sudo -l -U kirsty
```

The output should include a `NOPASSWD: ALL` rule.

### 6. Test passwordless sudo

```bash
sudo -u kirsty sudo -n whoami
```

Expected output:

```text
root
```

The `-n` option makes sudo non-interactive. It fails instead of prompting for a password, so successful output confirms that passwordless sudo is working.

## Lessons Learned

- Prefer a separate file under `/etc/sudoers.d/` instead of changing `/etc/sudoers`. It keeps user-specific rules isolated and easier to remove or audit.
- Always edit sudoers configurations through `visudo`, which checks syntax before saving.
- A malformed sudoers file can prevent administrative access, so `visudo -c` is an essential verification step.
- Passwordless sudo is powerful and removes an important authentication checkpoint. In production, grant only the commands genuinely required whenever possible.
- Correct sudoers permissions are normally `0440`. Files with unsafe ownership or permissions may be ignored.
- Adding a user to the `wheel` group usually grants sudo access, but it does not necessarily make sudo passwordless.

## Interview Questions

### 1. What is the purpose of `visudo`?

It safely edits sudoers configuration files, applies locking, and checks their syntax before accepting changes.

### 2. What does `NOPASSWD` mean?

It allows matching sudo commands to run without requesting the invoking user's password.

### 3. Why use `/etc/sudoers.d/`?

It separates custom rules from the main sudoers file, improving maintainability, automation, and auditing.

### 4. How would you allow only one command?

```sudoers
kirsty ALL=(root) NOPASSWD: /usr/bin/systemctl restart httpd
```

This follows least privilege by allowing only the specified command.

### 5. How can you list a user's sudo permissions?

```bash
sudo -l -U kirsty
```

### 6. What is the difference between `sudo` and `su`?

`sudo` authorizes specific commands according to policy and records their execution. `su` changes the current shell identity, commonly requiring the target user's password.

### 7. How do you revoke this access?

Remove or disable Kirsty's dedicated rule and validate the remaining configuration:

```bash
sudo rm /etc/sudoers.d/kirsty
sudo visudo -c
```
