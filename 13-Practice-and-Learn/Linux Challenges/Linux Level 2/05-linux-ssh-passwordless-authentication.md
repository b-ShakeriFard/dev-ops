# Linux Passwordless SSH Authentication

## Objective

Configure passwordless SSH authentication from user `thor` on the jump host to all Nautilus application servers through their respective sudo users.

| Server | Remote sudo user |
|---|---|
| `stapp01` | `tony` |
| `stapp02` | `steve` |
| `stapp03` | `banner` |

The goal is SSH authentication without repeatedly entering the remote account passwords. It does not necessarily configure passwordless `sudo` after login.

## 1. Check for an existing key

Run these commands as `thor` on the jump host:

```bash
whoami
ls -l ~/.ssh/*.pub
```

If a public key already exists, it can normally be reused. Do not overwrite an existing key unless its replacement is intentional.

## 2. Generate a key pair

If no key exists, generate an Ed25519 key pair:

```bash
ssh-keygen -t ed25519
```

Press Enter to accept the default path and leave the passphrase empty when the challenge requires fully passwordless access.

Do not use `sudo ssh-keygen`. That would generate a key for `root`, while the required source identity is `thor`.

The generated files are typically:

```text
~/.ssh/id_ed25519      # private key: never share
~/.ssh/id_ed25519.pub  # public key: safe to copy
```

## 3. Install the public key

Copy Thor's public key to each remote sudo user:

```bash
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```

Enter each remote user's password once. `ssh-copy-id` appends the public key to that user's remote file:

```text
~/.ssh/authorized_keys
```

It also normally creates the SSH directory and applies suitable permissions.

## 4. Verify passwordless authentication

Test each account while explicitly refusing password authentication:

```bash
ssh -o PasswordAuthentication=no tony@stapp01 'whoami'
ssh -o PasswordAuthentication=no steve@stapp02 'whoami'
ssh -o PasswordAuthentication=no banner@stapp03 'whoami'
```

Expected output:

```text
tony
steve
banner
```

If SSH still requests a password, inspect the authentication process:

```bash
ssh -v tony@stapp01
```

On the remote server, typical permissions are:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

The private key stays on the jump host. Only the public key is installed on the application servers.
