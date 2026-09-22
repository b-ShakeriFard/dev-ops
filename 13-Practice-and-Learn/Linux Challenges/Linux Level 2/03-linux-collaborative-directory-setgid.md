# Linux Collaborative Directory with Setgid

## Challenge

Configure `/sysops/data` on application server 2 as a collaborative directory for the `sysops` group.

Requirements:

- The directory must be group-owned by `sysops`.
- The owning user and group must have read, write, and execute permissions.
- Other users must have no access.
- New files and subdirectories must inherit the `sysops` group.

## Solution

Connect to application server 2 and confirm that the group exists:

```bash
ssh steve@stapp02
getent group sysops
```

Configure ownership and permissions:

```bash
sudo chown root:sysops /sysops/data
sudo chmod 2770 /sysops/data
```

Verify the result:

```bash
ls -ld /sysops/data
```

Expected output:

```text
drwxrws--- root sysops ... /sysops/data
```

## Understanding the ownership

Linux objects must have one owning user and one owning group. In this case:

```text
root:sysops
```

`root` is the owning user and `sysops` is the owning group. The challenge does not specify a different user owner, so retaining `root` is appropriate. The important security restriction is that ordinary users outside the `sysops` group receive no permissions.

To change only the group, this equivalent command can be used:

```bash
sudo chown :sysops /sysops/data
```

Running `chown sysops ...` without a colon treats `sysops` as a username and fails if no such user exists.

## Understanding `2770` and `rws`

```text
2  7  7  0
│  │  │  └─ Others: ---
│  │  └──── Group: rwx
│  └─────── Owner: rwx
└────────── Setgid
```

In `drwxrws---`, the group section is `rws`:

- `r` allows listing directory contents.
- `w` allows creating, deleting, and renaming entries.
- `s` represents execute permission plus the setgid bit.

Setgid makes new files and subdirectories inherit the directory's `sysops` group instead of the creator's primary group. A lowercase `s` means execute is enabled; uppercase `S` would mean setgid is present but execute is missing.

## Common mistake

These modes are equivalent:

```bash
chmod 70 /sysops/data
chmod 070 /sysops/data
```

Both produce `d---rwx---`: only the group has access. They do not satisfy the requirement that both the owning user and group have `rwx`, and they do not enable setgid.
