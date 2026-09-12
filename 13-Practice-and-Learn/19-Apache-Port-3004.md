# Apache Web Server on Port 3004

## Scenario

The task was to prepare **App Server 2** to host two static websites using Apache HTTP Server.

The requirements were:

- Install Apache (`httpd`) and its dependencies.
- Configure Apache to listen on port `3004`.
- Use two website backups located on the jump host:
  - `/home/thor/beta`
  - `/home/thor/demo`
- Serve them as:
  - `http://localhost:3004/beta/`
  - `http://localhost:3004/demo/`
- Verify both sites using `curl`.

---

## 1. Install Apache

On App Server 2:

```bash
sudo dnf install -y httpd
```

Enable Apache:

```bash
sudo systemctl enable httpd
```

---

## 2. Configure Apache to Listen on Port 3004

Edit the main Apache configuration file:

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

Find:

```apache
Listen 80
```

and change it to:

```apache
Listen 3004
```

Validate the configuration:

```bash
sudo httpd -t
```

Expected:

```text
Syntax OK
```

Then restart Apache:

```bash
sudo systemctl restart httpd
```

---

## 3. Verify the Listening Port

Check that Apache is listening on `3004`:

```bash
sudo ss -lntp | grep 3004
```

You should see `httpd` listening on that port.

---

## 4. Understand Apache's Document Root

The default Apache document root is:

```text
/var/www/html
```

A file placed here:

```text
/var/www/html/index.html
```

is served at:

```text
http://localhost:3004/
```

But the task requires two separate URL paths:

```text
/beta/
/demo/
```

So the filesystem should look like:

```text
/var/www/html/
├── beta/
│   └── index.html
└── demo/
    └── index.html
```

This maps naturally to:

```text
/var/www/html/beta/index.html
        ↓
http://localhost:3004/beta/

/var/www/html/demo/index.html
        ↓
http://localhost:3004/demo/
```

No special `VirtualHost` configuration is required for this simple directory-based setup.

---

## 5. Transfer the Website Files

From the jump host, copy the website directories to the application server:

```bash
scp -r /home/thor/beta steve@stapp02:/home/steve/
scp -r /home/thor/demo steve@stapp02:/home/steve/
```

The `-r` option means recursive and is required when copying directories.

---

## 6. Create the Web Directories

On App Server 2:

```bash
sudo mkdir -p /var/www/html/beta
sudo mkdir -p /var/www/html/demo
```

Then copy the transferred files into the Apache document root:

```bash
sudo cp -r /home/steve/beta/* /var/www/html/beta/
sudo cp -r /home/steve/demo/* /var/www/html/demo/
```

---

## 7. Verify the File Layout

Run:

```bash
ls -R /var/www/html
```

Expected:

```text
/var/www/html:
beta
demo

/var/www/html/beta:
index.html

/var/www/html/demo:
index.html
```

---

## 8. Test the Websites

Test the beta site:

```bash
curl http://localhost:3004/beta/
```

Test the demo site:

```bash
curl http://localhost:3004/demo/
```

Both commands should return the appropriate HTML content.

---

## Common Issue: Copying to `/home/`

An attempt such as:

```bash
scp file steve@stapp02:/home/
```

may fail with:

```text
Permission denied
```

because a normal user usually cannot write directly into `/home`.

Instead, copy into the user's own home directory:

```bash
scp file steve@stapp02:/home/steve/
```

or simply:

```bash
scp file steve@stapp02:
```

Then use `sudo` on the destination server to move or copy files into protected locations.

---

## Common Issue: Wrong URL Layout

If the beta site's `index.html` is copied directly to:

```text
/var/www/html/index.html
```

then it is served at:

```text
http://localhost:3004/
```

not:

```text
http://localhost:3004/beta/
```

To serve `/beta/`, the file must live under:

```text
/var/www/html/beta/
```

Likewise, `/demo/` maps to:

```text
/var/www/html/demo/
```

---

## Useful Commands

```bash
sudo dnf install -y httpd
sudo vi /etc/httpd/conf/httpd.conf
sudo httpd -t
sudo systemctl enable httpd
sudo systemctl restart httpd
sudo systemctl status httpd
sudo ss -lntp | grep 3004

scp -r /home/thor/beta steve@stapp02:/home/steve/
scp -r /home/thor/demo steve@stapp02:/home/steve/

sudo mkdir -p /var/www/html/beta
sudo mkdir -p /var/www/html/demo

sudo cp -r /home/steve/beta/* /var/www/html/beta/
sudo cp -r /home/steve/demo/* /var/www/html/demo/

curl http://localhost:3004/beta/
curl http://localhost:3004/demo/
```

---

## Key Takeaways

1. Apache on RHEL/CentOS is usually managed as the `httpd` service.
2. The listening port is configured with the `Listen` directive.
3. The default document root is `/var/www/html`.
4. URL paths often map directly to directories beneath the document root.
5. `scp -r` transfers directories recursively.
6. Normal users cannot usually write directly into protected paths such as `/home/` or `/var/www/html`.
7. Use `sudo` on the destination server when moving files into privileged locations.
8. Always verify the service, listening port, file layout, and HTTP response.

---

## Final Architecture

```text
Jump Host
   |
   | scp -r
   v
App Server 2
   |
   v
Apache httpd :3004
   |
   +--> /var/www/html/beta/index.html
   |         ↓
   |       /beta/
   |
   +--> /var/www/html/demo/index.html
             ↓
           /demo/
```

This challenge combined **Apache installation, custom port configuration, SCP file transfer, Linux permissions, document-root structure, and HTTP verification**.
