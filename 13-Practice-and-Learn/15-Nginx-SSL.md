# Nginx SSL/TLS Configuration with a Self-Signed Certificate

## Scenario

The task was to prepare **App Server 1** for a new application by installing and configuring Nginx with HTTPS.

The requirements were:

- Install Nginx.
- Deploy an existing self-signed SSL certificate and private key.
- Configure Nginx to serve HTTPS traffic.
- Create an `index.html` file containing:

```text
Welcome!
```

- Verify the website from the jump host using HTTPS.

The provided certificate files were:

```text
/tmp/nautilus.crt
/tmp/nautilus.key
```

---

# 1. Install Nginx

On a CentOS/RHEL-style system:

```bash
sudo dnf install -y nginx
```

Enable and start the service:

```bash
sudo systemctl enable --now nginx
```

Check the status:

```bash
sudo systemctl status nginx
```

---

# 2. Understand What “Configure Nginx” Means

Installing Nginx only places the software, default configuration files, service definition, and document root on the system.

Configuration determines how Nginx behaves.

Typical configuration decisions include:

```text
Listening ports
Server name
Document root
HTTPS/TLS settings
Certificate paths
Reverse proxy settings
Logging
Error pages
```

The main configuration file is commonly:

```text
/etc/nginx/nginx.conf
```

Additional configuration files may exist under:

```text
/etc/nginx/conf.d/
```

---

# 3. Inspect the Default Nginx Document Root

The default document root on this system was:

```text
/usr/share/nginx/html
```

Check its contents:

```bash
ls -l /usr/share/nginx/html
```

If `index.html` is a symbolic link, inspect it with:

```bash
ls -l /usr/share/nginx/html/index.html
```

or:

```bash
readlink -f /usr/share/nginx/html/index.html
```

To remove only the symbolic link:

```bash
sudo rm /usr/share/nginx/html/index.html
```

Removing a symlink does not delete the file it points to.

---

# 4. Create the Required Web Page

Create the requested index page:

```bash
echo 'Welcome!' | sudo tee /usr/share/nginx/html/index.html
```

Verify:

```bash
cat /usr/share/nginx/html/index.html
```

Expected:

```text
Welcome!
```

---

# 5. Prepare Certificate Directories

The default Nginx configuration already referenced these locations:

```text
/etc/pki/nginx/server.crt
/etc/pki/nginx/private/server.key
```

Create the directories if they do not exist:

```bash
sudo mkdir -p /etc/pki/nginx/private
```

The `-p` option creates parent directories as required and does not fail if the directory already exists.

---

# 6. Move the Certificate and Private Key

Move the provided files into the paths referenced by Nginx:

```bash
sudo mv /tmp/nautilus.crt /etc/pki/nginx/server.crt
sudo mv /tmp/nautilus.key /etc/pki/nginx/private/server.key
```

Protect the private key:

```bash
sudo chmod 600 /etc/pki/nginx/private/server.key
```

The private key should not be readable by ordinary users.

Verify:

```bash
sudo ls -l /etc/pki/nginx/server.crt
sudo ls -l /etc/pki/nginx/private/server.key
```

---

# 7. Configure the TLS Server Block

Edit:

```bash
sudo vim /etc/nginx/nginx.conf
```

The default configuration already contained a commented TLS-enabled server block.

A minimal working version looks like:

```nginx
server {
    listen       443 ssl;
    listen       [::]:443 ssl;

    server_name  _;
    root         /usr/share/nginx/html;

    ssl_certificate     "/etc/pki/nginx/server.crt";
    ssl_certificate_key "/etc/pki/nginx/private/server.key";

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 10m;
    ssl_ciphers PROFILE=SYSTEM;
    ssl_prefer_server_ciphers on;
}
```

Important directives:

```text
listen 443 ssl
```

Tells Nginx to listen for HTTPS connections on TCP port 443.

```text
root /usr/share/nginx/html
```

Defines the directory from which Nginx serves files.

```text
ssl_certificate
```

Points to the public certificate.

```text
ssl_certificate_key
```

Points to the corresponding private key.

---

# 8. A Common Configuration Mistake

While uncommenting the TLS block, an explanatory comment was accidentally uncommented:

```text
Settings for a TLS enabled server.
```

Nginx then reported:

```text
unknown directive "Settings"
```

The reason is that Nginx interpreted the English sentence as a configuration directive.

The correct line is:

```nginx
# Settings for a TLS enabled server.
```

This is an important lesson:

```text
Uncomment actual Nginx directives.
Keep descriptive text commented with #.
```

---

# 9. Validate the Configuration

Always test the configuration before restarting Nginx:

```bash
sudo nginx -t
```

Successful output should include:

```text
syntax is ok
test is successful
```

If there is an error, Nginx usually reports:

```text
file path
line number
problematic directive
```

This makes `nginx -t` one of the most useful troubleshooting commands for Nginx.

---

# 10. Apply the Configuration

After the syntax test passes:

```bash
sudo systemctl restart nginx
```

or:

```bash
sudo systemctl reload nginx
```

A reload is preferable when the service is already running and only configuration changes need to be applied.

---

# 11. Verify Port 443

Check that Nginx is listening:

```bash
sudo ss -lntp | grep 443
```

You should see Nginx bound to port 443.

---

# 12. Test HTTPS Locally

Because the certificate is self-signed, `curl` will not trust it by default.

Use:

```bash
curl -k https://localhost
```

The `-k` option tells `curl` to ignore certificate trust validation.

Expected content:

```text
Welcome!
```

To inspect only response headers:

```bash
curl -Ik https://localhost
```

---

# 13. Test from the Jump Host

From the jump host:

```bash
curl -k https://stapp01
```

or:

```bash
curl -Ik https://stapp01
```

This validates:

```text
DNS/hostname resolution
Network connectivity
TCP port 443
Nginx HTTPS configuration
Certificate/key deployment
Web content
```

---

# Troubleshooting Workflow

A clean troubleshooting sequence is:

```text
Install Nginx
      ↓
Inspect default config
      ↓
Prepare certificate directories
      ↓
Move certificate and key
      ↓
Configure TLS server block
      ↓
Create index.html
      ↓
Run nginx -t
      ↓
Fix syntax errors
      ↓
Reload/restart Nginx
      ↓
Check port 443
      ↓
Test with curl -k
```

---

# Useful Commands

```bash
sudo dnf install -y nginx

sudo systemctl enable --now nginx

sudo mkdir -p /etc/pki/nginx/private

sudo mv /tmp/nautilus.crt /etc/pki/nginx/server.crt
sudo mv /tmp/nautilus.key /etc/pki/nginx/private/server.key

sudo chmod 600 /etc/pki/nginx/private/server.key

echo 'Welcome!' | sudo tee /usr/share/nginx/html/index.html

sudo vim /etc/nginx/nginx.conf

sudo nginx -t

sudo systemctl reload nginx

sudo ss -lntp | grep 443

curl -k https://localhost
```

---

# Key Takeaways

1. Installing Nginx is not the same as configuring it.
2. HTTPS requires both a certificate and its matching private key.
3. The private key must be protected carefully.
4. Nginx configuration is sensitive to syntax.
5. Explanatory text must remain commented with `#`.
6. Always run:

```bash
sudo nginx -t
```

before reloading or restarting Nginx.

7. A self-signed certificate requires `curl -k` unless the certificate is explicitly trusted.
8. The default Nginx TLS block can often be reused instead of writing a configuration from scratch.
9. Validate the service at multiple layers: configuration, process, listening port, and HTTP response.

---

# Final Result

The final architecture was:

```text
Jump Host
   |
   | HTTPS
   v
App Server 1
   |
   | TCP 443
   v
Nginx
   |
   | SSL certificate + private key
   v
/usr/share/nginx/html/index.html
   |
   v
Welcome!
```

This challenge combined package installation, Linux filesystem permissions, TLS concepts, Nginx configuration, syntax validation, and network-level testing into one practical HTTPS deployment workflow.
