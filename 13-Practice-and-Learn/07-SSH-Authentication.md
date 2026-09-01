# Passwordless SSH Authentication

## Scenario

A management server needs to execute scripts automatically on several remote Linux servers.

The scripts run as user `thor` on the **jump host**, but must connect to different users on the application servers:

```text
thor@jump-host
   ├── tony@stapp01
   ├── steve@stapp02
   └── banner@stapp03
```

Since automated scripts cannot conveniently enter passwords every time, SSH key-based authentication is required.

---

## Goal

Allow `thor` on the jump host to execute:

```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

without entering the remote users' passwords.

---

## 1. Generate an SSH Key Pair

While logged in as `thor`:

```bash
ssh-keygen -t ed25519
```

Accept the default location:

```text
/home/thor/.ssh/id_ed25519
```

For fully passwordless automation, leave the passphrase empty.

This creates:

```text
~/.ssh/id_ed25519       # Private key
~/.ssh/id_ed25519.pub   # Public key
```

The **private key must remain on the jump host**.

---

## 2. Copy the Public Key

Install the public key for each remote user:

```bash
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```

The user's password is required once during this step.

`ssh-copy-id` adds the public key to:

```text
~/.ssh/authorized_keys
```

on the remote server.

---

## 3. Test Passwordless Login

Test each connection:

```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

If configured correctly, SSH should connect without requesting the remote account password.

---

## Common Problem: Custom Key Name

A key can be generated using a custom filename:

```bash
ssh-keygen -t ed25519
```

For example:

```text
superfile
superfile.pub
```

The public key can then be installed with:

```bash
ssh-copy-id -i superfile.pub tony@stapp01
```

However, login may require:

```bash
ssh -i superfile tony@stapp01
```

because SSH does not automatically search arbitrary filenames.

For automation and simple commands such as:

```bash
ssh tony@stapp01
```

using the standard location is usually preferable:

```text
~/.ssh/id_ed25519
```

---

## Useful Permissions

SSH is sensitive to file permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
```

---

## Key Takeaway

SSH key authentication works by keeping the **private key on the client** and placing the corresponding **public key on the server**.

```text
Private Key                     Public Key
Jump Host                       Remote Server
    │                                │
    └──── authentication proof ──────┘
```

For automated administration, always verify that the exact command the automation will execute works:

```bash
ssh user@server
```

not merely:

```bash
ssh -i custom-key user@server
```