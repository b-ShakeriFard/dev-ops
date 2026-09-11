# Nginx Load Balancer Configuration

## Scenario

The task was to configure **Nginx as a load balancer** on the Nautilus Load Balancer server (`stlb01`) and distribute incoming HTTP traffic across all application servers.

The requirements were:

- Install Nginx on the load balancer if needed.
- Use the main Nginx configuration file:

```text
/etc/nginx/nginx.conf
```

- Configure load balancing inside the `http` context.
- Use all application servers as backend servers.
- Do not change the Apache ports already configured on the app servers.
- Make sure Apache is running on all backend servers.
- Verify the setup with:

```bash
curl http://stlb01:80
```

---

## 1. Install Nginx

On the load balancer:

```bash
sudo dnf install -y nginx
```

Enable and start it:

```bash
sudo systemctl enable --now nginx
```

Check:

```bash
sudo systemctl status nginx
```

---

## 2. Find the Apache Backend Port

Before configuring Nginx, determine which port Apache is already using on the application servers.

SSH into an app server:

```bash
ssh tony@stapp01
```

Then inspect listening sockets:

```bash
sudo ss -ltnp | grep httpd
```

Example output:

```text
LISTEN 0 511 *:8087 *:* users:(("httpd",pid=...))
```

This means Apache is listening on:

```text
8087
```

Repeat or verify on the other app servers.

You can also inspect Apache configuration directly:

```bash
grep -R "^Listen" /etc/httpd/conf /etc/httpd/conf.d
```

---

## 3. Understand `ss` vs `netstat`

Both commands can inspect network sockets.

Modern Linux usually prefers:

```bash
ss -ltnp
```

Older systems often use:

```bash
netstat -ltnp
```

Useful option meanings:

```text
-l  → listening sockets
-t  → TCP sockets
-n  → numeric IPs and ports
-p  → owning process information
```

---

## 4. Configure the Nginx Upstream

Edit the main configuration file:

```bash
sudo vim /etc/nginx/nginx.conf
```

Inside the existing:

```nginx
http {
    ...
}
```

add an `upstream` block.

Example:

```nginx
upstream backend_servers {
    server stapp01:8087;
    server stapp02:8087;
    server stapp03:8087;
}
```

The `upstream` block must be:

- inside `http {}`
- outside the `server {}` block

Correct structure:

```nginx
http {

    upstream backend_servers {
        ...
    }

    server {
        ...
    }
}
```

---

## 5. Configure `proxy_pass`

Inside the existing Nginx server block:

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name _;

    location / {
        proxy_pass http://backend_servers;
    }
}
```

This tells Nginx:

> Forward incoming requests to the backend pool named `backend_servers`.

---

## 6. How the Load Balancing Works

The architecture is:

```text
                    Client
                      |
                      v
                  stlb01:80
                      |
                    Nginx
                      |
              backend_servers
              /      |      \
             v       v       v
        stapp01   stapp02   stapp03
         :8087     :8087     :8087
```

By default, Nginx uses **round-robin** load balancing.

Example:

```text
Request 1 → stapp01
Request 2 → stapp02
Request 3 → stapp03
Request 4 → stapp01
```

---

## 7. Syntax Validation

Before restarting Nginx, always test the configuration:

```bash
sudo nginx -t
```

Expected:

```text
syntax is ok
test is successful
```

This catches configuration mistakes before they break the service.

---

## 8. Troubleshooting Example: Missing Semicolon

A syntax error occurred because one backend line was written as:

```nginx
server stapp03:8087
```

instead of:

```nginx
server stapp03:8087;
```

Nginx reported:

```text
unexpected "}"
```

The reason was that the previous directive had not been terminated.

Nginx configuration directives generally end with:

```text
;
```

This is a very common syntax mistake.

---

## 9. Apply the Configuration

After `nginx -t` succeeds:

```bash
sudo systemctl restart nginx
```

or:

```bash
sudo systemctl reload nginx
```

Check status:

```bash
sudo systemctl status nginx
```

---

## 10. Test the Load Balancer

From the load balancer:

```bash
curl http://localhost
```

From the jump host:

```bash
curl http://stlb01:80
```

A successful response confirms that:

```text
Nginx is running
Port 80 is reachable
The upstream pool resolves
Backend Apache services are reachable
proxy_pass is working
```

---

## Useful Commands

```bash
sudo dnf install -y nginx

sudo systemctl enable --now nginx

sudo ss -ltnp | grep httpd

grep -R "^Listen" /etc/httpd/conf /etc/httpd/conf.d

sudo vim /etc/nginx/nginx.conf

sudo nginx -t

sudo systemctl restart nginx

sudo systemctl status nginx

curl http://localhost

curl http://stlb01:80
```

---

## Common Mistakes

### Putting `upstream` Inside `server`

Wrong:

```nginx
server {
    upstream backend_servers {
        ...
    }
}
```

Correct:

```nginx
http {
    upstream backend_servers {
        ...
    }

    server {
        ...
    }
}
```

### Forgetting the Semicolon

Wrong:

```nginx
server stapp03:8087
```

Correct:

```nginx
server stapp03:8087;
```

### Using the Wrong Backend Port

Do not assume Apache is on port `80` or `8080`.

Always verify:

```bash
sudo ss -ltnp | grep httpd
```

### Testing Nginx Without `sudo`

Running:

```bash
nginx -t
```

as a normal user may produce permission warnings for log files.

Prefer:

```bash
sudo nginx -t
```

---

## Key Takeaways

1. Nginx can act as both a web server and a reverse proxy/load balancer.
2. The `upstream` block defines the backend server pool.
3. `proxy_pass` forwards client requests to that pool.
4. Backend ports should be discovered, not guessed.
5. `ss -ltnp` is a modern replacement for many `netstat` use cases.
6. Nginx configuration syntax is strict; missing semicolons matter.
7. Always validate with:

```bash
sudo nginx -t
```

before restarting or reloading Nginx.
8. Load balancing only works if the backend Apache services are healthy and reachable.

---

## Final Configuration Example

```nginx
http {

    upstream backend_servers {
        server stapp01:8087;
        server stapp02:8087;
        server stapp03:8087;
    }

    server {
        listen 80;
        listen [::]:80;
        server_name _;

        location / {
            proxy_pass http://backend_servers;
        }
    }
}
```

This challenge was a practical introduction to **reverse proxying, backend discovery, load balancing, configuration validation, and troubleshooting Nginx syntax errors**.
