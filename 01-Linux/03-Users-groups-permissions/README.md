# 👥 Linux Users, Groups & Permissions

## Overview

Linux is a multi-user operating system designed to support multiple users, applications, and services running simultaneously.

To maintain security and organization, Linux uses a permission model based on **users**, **groups**, and **ownership**.

Every process running on a Linux system has an associated user identity, and every file or directory has ownership information that determines who can access and modify it.

Understanding users, groups, and permissions is fundamental for Linux administration because it controls system security, application access, and resource management.

---

# 👤 Users in Linux

A **user** represents an identity that can interact with the Linux system.

Users can be:

- Human users
- System users
- Service accounts

Examples:

| User Type | Purpose |
|---|---|
| Regular user | Human account for daily activities |
| Root user | Superuser with unrestricted privileges |
| System user | Account used by services and applications |

Each user has important attributes:

- Username
- User ID (UID)
- Primary group
- Home directory
- Default shell

Example:

```text
username:x:1001:1001:User Name:/home/username:/bin/bash
```

User information is stored mainly in:

```text
/etc/passwd
/etc/shadow
```

---

# 🔑 Root User and Privileges

Linux has a special administrative account called **root**.

The root user has complete control over the system:

- Install and remove software
- Modify system configuration
- Manage users
- Access all files
- Control system services

Because root has unlimited privileges, administrators usually use controlled privilege escalation through tools such as:

```bash
sudo
```

This approach improves security and accountability.

---

# 👥 Groups in Linux

A **group** is a collection of users who share common permissions.

Groups simplify administration by allowing permissions to be assigned to multiple users at once.

Examples:

```text
Developers
Database Administrators
System Administrators
Docker Users
```

Each user has:

- One primary group
- Zero or more secondary groups

Group information is stored in:

```text
/etc/group
```

---

# 🔐 File Ownership Model

Every file and directory in Linux has:

- Owner
- Group owner
- Permission settings

Example:

```bash
ls -l file.txt
```

Output:

```text
-rw-r----- 1 alice developers file.txt
```

Meaning:

```
Owner  → alice
Group  → developers
```

Permissions are divided into three categories:

| Category | Applies To |
|---|---|
| User | File owner |
| Group | Group members |
| Others | Everyone else |

---

# 🛡️ Linux Permission System

Linux permissions determine what actions users can perform.

The three basic permissions are:

| Permission | Meaning |
|---|---|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

Example:

```text
-rwxr-xr--
```

Breakdown:

```text
Owner     rwx
Group     r-x
Others    r--
```

This model provides fine-grained control over system resources.

---

# 🔄 Permission Management

Linux administrators manage permissions using several concepts:

## Ownership

Changing ownership:

```bash
chown
```

Example:

```bash
chown user file.txt
```

---

## Group Ownership

Changing group ownership:

```bash
chgrp
```

Example:

```bash
chgrp developers file.txt
```

---

## Permission Modification

Changing permissions:

```bash
chmod
```

Example:

```bash
chmod 750 script.sh
```

---

# ⚙️ User Administration Tasks

Linux administrators commonly perform:

- Creating users
- Removing users
- Changing passwords
- Managing groups
- Assigning permissions
- Configuring sudo access
- Managing service accounts

Common commands include:

```bash
useradd
usermod
userdel
passwd
groupadd
groupmod
groupdel
id
whoami
groups
```

---

# 🏢 Users and Groups in Enterprise Linux

In enterprise environments, user management often integrates with centralized identity systems:

- LDAP
- Active Directory
- Kerberos
- Identity Management (IdM)

This allows organizations to manage thousands of users across many Linux servers using a unified authentication system.

---

# Conclusion

Users, groups, and permissions form the security foundation of Linux.

A Linux administrator must understand not only how to create accounts, but also how identities, ownership, and permissions interact to protect system resources.

Mastering this area is essential before moving into advanced topics such as system security, containers, Kubernetes RBAC, and enterprise authentication systems.
