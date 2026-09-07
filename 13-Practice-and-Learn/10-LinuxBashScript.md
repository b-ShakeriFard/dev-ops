# Bash Script Challenge - Archive and Transfer

## Scenario

The task was to create a Bash script on **App Server 1** that:

- Archives the directory `/var/www/html/media`
- Creates a ZIP file named `xfusioncorp_media.zip`
- Stores the archive under `/archives/`
- Copies the archive to the Nautilus Storage Server
- Uses `scp` without asking for a password
- Does **not** use `sudo` inside the script

The script was saved as:

```text
/scripts/media_archive.sh
```

---

## 1. Install the Required `zip` Package

Because the task requires a `.zip` archive, the `zip` utility must be installed.

On CentOS/RHEL-style systems:

```bash
sudo dnf install -y zip
```

or:

```bash
sudo yum install -y zip
```

This installation is done **outside the script**.

---

## 2. Prepare the Local Archive Directory

The required destination is:

```text
/archives/
```

A normal user such as `tony` cannot usually create directories directly under `/`.

So prepare the directory once with administrative privileges:

```bash
sudo mkdir -p /archives
sudo chown tony:tony /archives
```

### Why?

The script itself must not contain `sudo`, so `tony` needs permission to write into `/archives`.

---

## 3. Configure Passwordless SSH

The script must copy the archive to the storage server without asking for a password.

Generate an SSH key pair as `tony`:

```bash
ssh-keygen -t ed25519
```

Use the default location:

```text
/home/tony/.ssh/id_ed25519
```

Leave the passphrase empty if the task requires fully unattended execution.

Copy the public key to the storage server user:

```bash
ssh-copy-id natasha@ststor01
```

Test:

```bash
ssh natasha@ststor01
```

If login succeeds without asking for a password, `scp` can also work non-interactively.

---

## 4. Prepare the Remote Archive Directory

On the storage server, make sure `/archives` exists and is writable by `natasha`.

For example:

```bash
sudo mkdir -p /archives
sudo chown natasha:natasha /archives
```

---

## 5. Create the Bash Script

Create:

```bash
vi /scripts/media_archive.sh
```

Use:

```bash
#!/bin/bash

zip -r /archives/xfusioncorp_media.zip /var/www/html/media

scp /archives/xfusioncorp_media.zip natasha@ststor01:/archives/
```

---

## 6. Understanding the Commands

### Shebang

```bash
#!/bin/bash
```

This tells Linux to execute the script using Bash.

---

### `zip -r`

```bash
zip -r /archives/xfusioncorp_media.zip /var/www/html/media
```

Breakdown:

```text
zip    → creates a ZIP archive
-r     → recursive; include directories and all files inside them
output → /archives/xfusioncorp_media.zip
source → /var/www/html/media
```

The `-r` option is necessary because `media` is a directory.

---

### `scp`

```bash
scp /archives/xfusioncorp_media.zip natasha@ststor01:/archives/
```

Breakdown:

```text
scp                          → secure file copy over SSH
/archives/xfusioncorp_media.zip
                             → local source file
natasha@ststor01             → remote user and server
:/archives/                  → destination directory
```

The colon after the hostname is important because it identifies the destination as remote.

---

## 7. Make the Script Executable

```bash
chmod +x /scripts/media_archive.sh
```

Verify:

```bash
ls -l /scripts/media_archive.sh
```

You should see execute permission, for example:

```text
-rwxr-xr-x
```

---

## 8. Run the Script

Run it as the intended user:

```bash
/scripts/media_archive.sh
```

The script should:

1. Create the ZIP archive
2. Store it locally in `/archives`
3. Copy it to `ststor01`
4. Finish without prompting for a password

---

## 9. Verify the Local Archive

```bash
ls -l /archives/xfusioncorp_media.zip
```

---

## 10. Verify the Remote Archive

```bash
ssh natasha@ststor01 'ls -l /archives/xfusioncorp_media.zip'
```

If the file exists and SSH does not ask for a password, the task is complete.

---

# Common Problems Encountered

## Wrong Path: `/archive` vs `/archives`

A small typo caused:

```text
zip error: Could not create output file
```

The required path was:

```text
/archives/
```

not:

```text
/archive/
```

Always verify exact paths in automation tasks.

---

## Permission Denied Creating `/archives`

This failed as a normal user:

```bash
mkdir -p /archives
```

because creating a directory directly under `/` normally requires root privileges.

The correct approach was to prepare it outside the script:

```bash
sudo mkdir -p /archives
sudo chown tony:tony /archives
```

---

## `ssh-copy-id` and `.ssh`

If the user's SSH directory does not exist:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

Then generate or move the SSH keys into the standard location.

Using the standard key path allows commands such as:

```bash
ssh natasha@ststor01
scp file natasha@ststor01:/archives/
```

to work without needing `-i`.

---

# Troubleshooting Flow

```text
Script requirement
      ↓
Install zip
      ↓
Prepare /archives
      ↓
Set correct ownership
      ↓
Configure SSH keys
      ↓
Test passwordless SSH
      ↓
Write Bash script
      ↓
Make it executable
      ↓
Run script
      ↓
Verify local archive
      ↓
Verify remote archive
```

---

## Final Script

```bash
#!/bin/bash

zip -r /archives/xfusioncorp_media.zip /var/www/html/media

scp /archives/xfusioncorp_media.zip natasha@ststor01:/archives/
```

---

## Key Takeaways

1. Use `zip -r` when archiving a directory.
2. Prepare privileged directories outside a script when `sudo` is forbidden inside it.
3. `scp` uses SSH authentication.
4. Passwordless `scp` requires working SSH key-based authentication.
5. Use standard SSH key locations when automation must run without extra flags.
6. Verify both local and remote results after running an automation script.
7. Small path mistakes can break an otherwise correct script.

This challenge combines Bash scripting, Linux permissions, archiving, SSH authentication, and secure file transfer into one practical automation workflow.
