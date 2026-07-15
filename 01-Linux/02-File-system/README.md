# 📁 Linux File System

## Overview

The Linux filesystem is the foundation that defines how data is organized, stored, and accessed inside a Linux system.

Unlike operating systems that use separate drive letters, Linux follows a single hierarchical directory structure that starts from the root directory (`/`). Every file, directory, device, and system resource exists within this unified structure.

For Linux administrators, understanding the filesystem is essential because applications, services, configurations, logs, and user data all depend on this organization.

---

# 🌳 Linux Directory Hierarchy

Linux follows the **Filesystem Hierarchy Standard (FHS)**, which defines the purpose of important directories.

| Directory | Purpose |
|---|---|
| `/` | Root directory; the starting point of the entire filesystem |
| `/bin` | Essential user commands |
| `/sbin` | System administration commands |
| `/etc` | System-wide configuration files |
| `/home` | User home directories |
| `/root` | Administrator's home directory |
| `/var` | Variable data such as logs and application data |
| `/tmp` | Temporary files |
| `/usr` | Applications and shared system resources |
| `/opt` | Optional third-party software |
| `/dev` | Device files representing hardware |
| `/proc` | Virtual filesystem containing process and kernel information |
| `/sys` | Virtual filesystem exposing hardware and kernel information |
| `/boot` | Files required for system startup |

---

# 📄 Everything is a File

One of Linux's fundamental design concepts is:

> "Everything is a file."

Linux represents many system components through the filesystem interface.

Examples:

- Regular files → documents, scripts, binaries, configurations
- Directories → containers for organizing files
- Device files → hardware interfaces
- Virtual filesystems → kernel and process information

This unified approach allows administrators and applications to interact with different system components using familiar filesystem operations.

---

# 🔐 File Permissions and Ownership

Linux uses a permission-based security model to control access to files and directories.

Every file has:

- An owner
- A group owner
- Permission rules

Permissions are assigned to three categories:

| Category | Description |
|---|---|
| User | The file owner |
| Group | Users belonging to the file group |
| Others | All remaining users |

The three basic permissions are:

| Permission | Meaning |
|---|---|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

Understanding permissions is one of the most important skills for Linux administrators because security, application behavior, and user access depend on them.

---

# 💾 Filesystem Mounting

Linux separates physical storage devices from the directory structure through the concept of **mounting**.

A filesystem becomes available when it is attached to a directory called a **mount point**.

Example:

```bash
mount /dev/sdb1 /data
```

Important concepts:

- Storage devices
- Partitions
- Filesystems
- Mount points
- Automatic mounting during system startup
- `/etc/fstab`

Mounting allows Linux systems to manage different storage resources through a consistent directory structure.

---

# 🛠️ Essential Filesystem Commands

Linux administrators frequently use these commands to explore and manage filesystems:

| Command | Purpose |
|---|---|
| `pwd` | Display current directory |
| `ls` | List files and directories |
| `cd` | Navigate between directories |
| `tree` | Display directory structure |
| `stat` | Show file metadata |
| `file` | Identify file types |
| `df` | Display filesystem usage |
| `du` | Display directory size |
| `find` | Search for files |
| `mount` | Attach filesystems |
| `umount` | Detach filesystems |
| `lsblk` | Display block devices |

---

# 🐧 Linux Filesystem in System Administration

A Linux administrator interacts with the filesystem constantly:

- Installing applications
- Managing configuration files
- Reviewing logs
- Controlling user access
- Managing services
- Preparing storage for applications

A strong understanding of the filesystem provides the foundation required for advanced topics such as containers, virtualization, and Kubernetes storage.

---

# Conclusion

The Linux filesystem is more than a method of organizing files; it is the foundation of how Linux represents and manages the entire operating system.

Understanding directory structure, file types, permissions, and mounting concepts allows administrators to confidently manage servers and troubleshoot applications.

Mastering the filesystem is one of the first major steps toward becoming an effective Linux System Administrator.
