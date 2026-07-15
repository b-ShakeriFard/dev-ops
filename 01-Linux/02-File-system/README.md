<h1> Linux File System </h1>

Everything in Linux is represented as a file or treated through a file-like interface. This design provides a consistent way to manage regular files, directories, devices, processes, and system resources.

Understanding the filesystem hierarchy is essential for every Linux administrator because system configuration, applications, logs, user data, and hardware devices all depend on this structure.

<hr>

<h3> Linux Directory Hierarchy </h3>

A typical Linux system follows the Filesystem Hierarchy Standard (FHS), which defines the purpose of common directories.

Important directories include:


| Directory | Purpose                                                     |
| --------- | ----------------------------------------------------------- |
| `/`       | Root directory; starting point of the entire filesystem     |
| `/bin`    | Essential user commands                                     |
| `/sbin`   | Essential system administration commands                    |
| `/etc`    | System-wide configuration files                             |
| `/home`   | User home directories                                       |
| `/root`   | Administrator's home directory                              |
| `/var`    | Variable data such as logs and databases                    |
| `/tmp`    | Temporary files                                             |
| `/usr`    | User applications and shared resources                      |
| `/opt`    | Optional third-party software                               |
| `/dev`    | Device files                                                |
| `/proc`   | Virtual filesystem providing process and kernel information |
| `/sys`    | Virtual filesystem exposing kernel and hardware information |
| `/boot`   | Files required for system startup                           |

File Types in Linux

Linux identifies objects using file types. Common types include:

Regular files: documents, scripts, binaries, configuration files
Directories: containers for organizing files
Symbolic links: references pointing to another file or directory
Device files: interfaces representing hardware devices
Sockets: communication endpoints between processes
Named pipes: special files used for inter-process communication
File Permissions and Ownership

Linux uses a permission model to control access to files and directories.

Every file has:

An owner
A group owner
Permission rules for:
User
Group
Others

The three basic permissions are:

| Permission | Meaning |
| ---------- | ------- |
| `r`        | Read    |
| `w`        | Write   |
| `x`        | Execute |

<hr>

Filesystem Mounting

Linux separates storage devices from the directory structure through mounting.

A filesystem becomes accessible when it is attached to a directory called a mount point.

Examples:

mount /dev/sdb1 /data

Common storage-related concepts:

Disk partitions
Filesystems
Mount points
/etc/fstab
Automatic mounting during boot
