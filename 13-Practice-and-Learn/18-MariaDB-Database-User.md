# MariaDB Database and User Setup

## Scenario

The task was to configure MariaDB by creating:

- a new database
- a dedicated database user
- a password for that user
- full privileges for that user on the new database

The work was performed from the MariaDB shell on the database server.

---

## 1. Open the MariaDB Shell

On the database server:

```bash
sudo mariadb
```

A successful connection opens the MariaDB prompt:

```text
MariaDB [(none)]>
```

---

## 2. Create the Database

Create the required database:

```sql
CREATE DATABASE kodekloud_db10;
```

Verify:

```sql
SHOW DATABASES;
```

---

## 3. Create the Database User

Create a local MariaDB user:

```sql
CREATE USER 'kodekloud_pop'@'localhost'
IDENTIFIED BY 'your_password';
```

`'kodekloud_pop'` is the database username and `'localhost'` restricts the account to local connections from the database server.

---

## 4. Grant Privileges

Give the user full access to the new database:

```sql
GRANT ALL PRIVILEGES
ON kodekloud_db10.*
TO 'kodekloud_pop'@'localhost';
```

The expression:

```text
kodekloud_db10.*
```

means every table and object inside that database.

This does not grant administrative privileges over all MariaDB databases.

---

## 5. Reload and Verify Privileges

Run:

```sql
FLUSH PRIVILEGES;
```

Then verify:

```sql
SHOW GRANTS FOR 'kodekloud_pop'@'localhost';
```

You should see privileges associated with:

```text
kodekloud_db10.*
```

---

# Troubleshooting: Getting Stuck at the `'>` Prompt

During the task, the MariaDB client changed from:

```text
MariaDB [(none)]>
```

to:

```text
'>
```

This normally means MariaDB believes a quoted string has not been closed.

For example:

```sql
GRANT ALL PRIVILEGES ON kodekloud_db10.*
TO 'kodekloud_pop'@'localhost;
```

is missing the closing quote after `localhost`.

MariaDB therefore waits for more input instead of executing the statement.

---

## Clear an Incomplete Statement

The MariaDB client supports:

```text
\c
```

to clear the current unfinished statement.

After cancelling the incomplete command, the normal prompt should return:

```text
MariaDB [(none)]>
```

Then re-enter the SQL statement carefully.

---

## Why `EXIT;` May Not Work

When the prompt is:

```text
'>
```

the client still believes it is inside an unfinished quoted string.

Therefore:

```sql
EXIT;
```

may be treated as ordinary text inside that unfinished statement instead of as an exit command.

The incomplete input must first be cancelled or completed.

---

# Final Command Sequence

```bash
sudo mariadb
```

Then:

```sql
CREATE DATABASE kodekloud_db10;

CREATE USER 'kodekloud_pop'@'localhost'
IDENTIFIED BY 'your_password';

GRANT ALL PRIVILEGES
ON kodekloud_db10.*
TO 'kodekloud_pop'@'localhost';

FLUSH PRIVILEGES;

SHOW GRANTS FOR 'kodekloud_pop'@'localhost';

EXIT;
```

---

# Optional Login Test

Test the new account:

```bash
mariadb -u kodekloud_pop -p
```

Then:

```sql
USE kodekloud_db10;
```

If successful, the account can access the intended database.

---

## Key Takeaways

1. `CREATE DATABASE` creates a database.
2. `CREATE USER` creates a MariaDB account.
3. `'user'@'localhost'` specifies both the account name and allowed connection source.
4. `GRANT ALL PRIVILEGES ON database.*` grants full rights on that database only.
5. SQL statements normally end with `;`.
6. Quotes must be balanced correctly.
7. A `'>` prompt usually means an unfinished quoted string.
8. `\c` clears an unfinished SQL statement.
9. `SHOW GRANTS` is a useful final verification step.

This challenge was a practical introduction to **MariaDB database creation, user management, privileges, and SQL-shell troubleshooting**.
