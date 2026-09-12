# Nginx + PHP-FPM Integration on Port 8095

## Scenario

The task was to configure **Nginx** on `stapp03` to serve a PHP application using **PHP-FPM 8.1**.

The requirements were:

- Install and configure Nginx.
- Make Nginx listen on port `8095`.
- Use `/var/www/html` as the document root.
- Install PHP-FPM 8.1.
- Configure PHP-FPM to use the Unix socket:

```text
/var/run/php-fpm/default.sock
```

- Configure Nginx and PHP-FPM to work together.
- Verify the application from the jump host using:

```bash
curl http://stapp03:8095/index.php
```

The existing PHP files under `/var/www/html` were not to be modified.

---

## 1. Install Nginx

```bash
sudo dnf install -y nginx
sudo systemctl enable --now nginx
```

---

## 2. Configure Nginx

Edit:

```bash
sudo vim /etc/nginx/nginx.conf
```

Configure the server block:

```nginx
server {
    listen 8095;
    listen [::]:8095;

    server_name _;
    root /var/www/html;
}
```

Validate:

```bash
sudo nginx -t
```

Then restart:

```bash
sudo systemctl restart nginx
```

---

## 3. Verify Port 8095

If `ss` is available:

```bash
sudo ss -lntp | grep 8095
```

If not, install `net-tools`:

```bash
sudo dnf install -y net-tools
```

Then:

```bash
sudo netstat -lntp | grep 8095
```

The output should show Nginx listening on port `8095`.

---

## 4. Install PHP-FPM 8.1

Check available PHP streams:

```bash
sudo dnf module list php
```

Enable PHP 8.1:

```bash
sudo dnf module reset php -y
sudo dnf module enable php:8.1 -y
```

Install PHP-FPM:

```bash
sudo dnf install -y php-fpm php-cli
```

Verify:

```bash
php -v
```

---

## 5. Enable PHP-FPM

```bash
sudo systemctl enable --now php-fpm
sudo systemctl status php-fpm
```

Expected:

```text
active (running)
```

---

## 6. Configure the Unix Socket

Edit:

```bash
sudo vim /etc/php-fpm.d/www.conf
```

Set:

```ini
listen = /var/run/php-fpm/default.sock
```

Restart PHP-FPM:

```bash
sudo systemctl restart php-fpm
```

Verify:

```bash
ls -l /var/run/php-fpm/
```

You should see:

```text
default.sock
```

Do **not** manually create the socket with `touch`. PHP-FPM creates it automatically when the service starts.

---

## 7. Connect Nginx to PHP-FPM

Inside the Nginx `server` block, add:

```nginx
location ~ \.php$ {
    include fastcgi_params;
    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

A complete example:

```nginx
server {
    listen 8095;
    listen [::]:8095;

    server_name _;
    root /var/www/html;
    index index.php index.html;

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

---

## 8. How Nginx and PHP-FPM Work Together

```text
Client requests /index.php
        |
        v
Nginx receives request on :8095
        |
        v
Nginx matches the PHP location block
        |
        v
fastcgi_pass
        |
        v
/var/run/php-fpm/default.sock
        |
        v
PHP-FPM executes index.php
        |
        v
Result returned to Nginx
        |
        v
Client receives response
```

Nginx itself does not execute PHP. It forwards PHP requests to PHP-FPM using FastCGI.

---

## 9. Validate and Restart

Test Nginx:

```bash
sudo nginx -t
```

Then restart both services:

```bash
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```

Check:

```bash
sudo systemctl status php-fpm
sudo systemctl status nginx
```

---

## 10. Test the Application

Local test:

```bash
curl http://localhost:8095/index.php
```

From the jump host:

```bash
curl http://stapp03:8095/index.php
```

If the expected PHP-generated output is returned, the integration is working.

---

## Troubleshooting Notes

### `php8.1-fpm` Package Not Found

This may fail:

```bash
sudo dnf install php8.1-fpm
```

On RHEL/CentOS systems, the version is often selected through a DNF module stream instead:

```bash
sudo dnf module enable php:8.1 -y
sudo dnf install -y php-fpm
```

### `default.sock` Missing

Check the active PHP-FPM socket setting:

```bash
grep -n '^listen =' /etc/php-fpm.d/www.conf
```

Then restart:

```bash
sudo systemctl restart php-fpm
```

### `ss` Not Available

Install `net-tools` and use:

```bash
sudo netstat -lntp
```

---

## Useful Commands

```bash
sudo dnf install -y nginx
sudo nginx -t
sudo systemctl enable --now nginx
sudo netstat -lntp | grep 8095

sudo dnf module list php
sudo dnf module reset php -y
sudo dnf module enable php:8.1 -y
sudo dnf install -y php-fpm php-cli

sudo systemctl enable --now php-fpm
sudo vim /etc/php-fpm.d/www.conf
sudo systemctl restart php-fpm

ls -l /var/run/php-fpm/

sudo nginx -t
sudo systemctl restart nginx

curl http://localhost:8095/index.php
curl http://stapp03:8095/index.php
```

---

## Key Takeaways

1. Nginx serves HTTP requests but does not execute PHP itself.
2. PHP code is executed by PHP-FPM.
3. Nginx communicates with PHP-FPM through FastCGI.
4. A Unix socket can be used instead of a TCP port for local IPC.
5. The PHP-FPM `listen` directive defines the socket path.
6. Nginx `fastcgi_pass` must point to that same socket.
7. `SCRIPT_FILENAME` tells PHP-FPM which PHP file to execute.
8. Always validate Nginx configuration with:

```bash
sudo nginx -t
```

9. Restart PHP-FPM after changing its socket configuration.
10. Verify each layer separately: service, socket, Nginx config, and HTTP response.

---

## Final Architecture

```text
Jump Host
   |
   | HTTP :8095
   v
stapp03
   |
   v
Nginx
   |
   | FastCGI via Unix socket
   v
/var/run/php-fpm/default.sock
   |
   v
PHP-FPM 8.1
   |
   v
/var/www/html/index.php
```

This challenge was a practical introduction to **Nginx, PHP-FPM, FastCGI, Unix sockets, configuration validation, and PHP application delivery**.
