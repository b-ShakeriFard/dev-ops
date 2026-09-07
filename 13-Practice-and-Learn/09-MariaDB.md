# MariaDB Troubleshooting

## Scenario

A MariaDB service on a Linux server was not starting correctly.

The troubleshooting goal was to determine why the service failed, fix the underlying issue, and verify that MariaDB was running and listening on its default database port.

The main clues came from:

```bash
systemctl status mariadb
```

and:

```bash
journalctl
```

The important error indicated that MariaDB's data directory was not initialized correctly.

---

## 1. Check MariaDB Service Status

Start with:

```bash
sudo systemctl status mariadb
```

If the service is not running, try:

```bash
sudo systemctl start mariadb
```

If startup fails, inspect the logs.

---

## 2. Inspect MariaDB Logs

Useful commands:

```bash
sudo journalctl -u mariadb -n 50
```

or:

```bash
sudo journalctl -xeu mariadb
```

In this challenge, the logs indicated that the MariaDB database was not initialized correctly and referenced the data directory:

```text
/var/lib/mysql
```

---

## 3. Understand the MariaDB Data Directory

MariaDB normally stores database files under:

```text
/var/lib/mysql
```

The MariaDB service usually runs as the Linux user:

```text
mysql
```

Therefore, the database files and directories must normally be accessible to:

```text
mysql:mysql
```

Check ownership:

```bash
sudo ls -ld /var/lib/mysql
```

Inspect the contents:

```bash
sudo ls -la /var/lib/mysql
```

---

## 4. Fix Ownership

If the data directory has incorrect ownership, fix it with:

```bash
sudo chown -R mysql:mysql /var/lib/mysql
```

### Command Breakdown

```text
chown         → change file/directory ownership
-R            → apply recursively
mysql:mysql   → owner=mysql, group=mysql
/var/lib/mysql → MariaDB data directory
```

This command changes the ownership of the directory and everything inside it.

That matters because MariaDB needs permission to read and write its own database files.

---

## 5. Initialize the Database if Required

If MariaDB is installed but the data directory has not been initialized, use:

```bash
sudo mariadb-install-db --user=mysql --datadir=/var/lib/mysql
```

On some older systems, the equivalent command may be:

```bash
sudo mysql_install_db --user=mysql --datadir=/var/lib/mysql
```

This creates the initial system tables and database structure required by MariaDB.

> Important: Never blindly delete `/var/lib/mysql` on a real production database server. It may contain valuable databases.

---

## 6. Start MariaDB Again

After correcting ownership and initialization:

```bash
sudo systemctl start mariadb
```

Then verify:

```bash
sudo systemctl status mariadb
```

Expected:

```text
active (running)
```

---

## 7. Verify Port 3306

MariaDB normally listens on TCP port:

```text
3306
```

Check with:

```bash
sudo ss -lntp | grep 3306
```

A successful result should show `mariadbd` listening on the port.

Example:

```text
LISTEN ... *:3306 ... mariadbd
```

---

## 8. Optional SQL Connectivity Check

If the service is running, test local access:

```bash
sudo mariadb -e "SELECT VERSION();"
```

or:

```bash
mariadb -u root -p
```

If the query returns successfully, the database server is accepting connections.

---

# Troubleshooting Flow

```text
MariaDB unavailable
        ↓
Check systemctl status
        ↓
Try to start service
        ↓
Startup fails
        ↓
Read journal logs
        ↓
Inspect /var/lib/mysql
        ↓
Check ownership
        ↓
Fix mysql:mysql ownership
        ↓
Initialize database if needed
        ↓
Start MariaDB
        ↓
Verify port 3306
        ↓
Test SQL connection
```

---

# Useful Commands

```bash
sudo systemctl status mariadb
sudo systemctl start mariadb

sudo journalctl -u mariadb -n 50
sudo journalctl -xeu mariadb

sudo ls -ld /var/lib/mysql
sudo ls -la /var/lib/mysql

sudo chown -R mysql:mysql /var/lib/mysql

sudo mariadb-install-db --user=mysql --datadir=/var/lib/mysql

sudo systemctl restart mariadb

sudo ss -lntp | grep 3306

sudo mariadb -e "SELECT VERSION();"
```

---

## Key Takeaways

1. Always start troubleshooting with `systemctl status`.
2. Use `journalctl` to find the real startup error.
3. MariaDB normally stores its data in `/var/lib/mysql`.
4. The MariaDB process usually runs as the `mysql` user.
5. Incorrect ownership can prevent the database from starting.
6. `chown -R mysql:mysql /var/lib/mysql` is a common fix when ownership is broken.
7. If the database is not initialized, use `mariadb-install-db`.
8. Verify success with both service status and port `3306`.

---

## Final Result

The successful state looked like:

```text
MariaDB service: active (running)
Data directory:  /var/lib/mysql
Ownership:       mysql:mysql
Listening port:  3306
```

This challenge was a good example of troubleshooting a Linux service by moving from:

```text
service status → logs → filesystem permissions → initialization → network verification
```
