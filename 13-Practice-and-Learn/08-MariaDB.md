# MariaDB Startup Troubleshooting

## Scenario

MariaDB was installed on the database server, but the service failed to start.

The first step was to check the service status:

```bash
sudo systemctl status mariadb
```

Check the Data Directory

MariaDB normally stores its database files under:

/var/lib/mysql

Check the directory:

sudo ls -la /var/lib/mysql
sudo ls -ld /var/lib/mysql

MariaDB usually runs as the Linux user and group:

mysql:mysql

If ownership is incorrect, fix it with:

sudo chown -R mysql:mysql /var/lib/mysql

The -R option applies the ownership change recursively to all files and directories inside /var/lib/mysql.

Initialize MariaDB

If the data directory has not yet been initialized, run:

sudo mariadb-install-db \
  --user=mysql \
  --datadir=/var/lib/mysql

After initialization, start MariaDB:

sudo systemctl start mariadb

Enable it to start automatically after reboot:

sudo systemctl enable mariadb
Verify the Service

Check the status again:

sudo systemctl status mariadb

Expected result:

Active: active (running)

MariaDB normally listens on TCP port 3306.

Verify it with:

sudo ss -ltnp | grep 3306

A successful result should show mariadbd listening on port 3306.

Optionally test the database directly:

sudo mariadb -e "SELECT VERSION();"
Troubleshooting Flow
MariaDB fails to start
        |
        v
systemctl status mariadb
        |
        v
Read the startup error
        |
        v
Check /var/lib/mysql
        |
        v
Check mysql:mysql ownership
        |
        v
Initialize database if required
        |
        v
Start MariaDB
        |
        v
Verify TCP 3306
Key Takeaway

When a database service fails, avoid immediately reinstalling packages or deleting files.