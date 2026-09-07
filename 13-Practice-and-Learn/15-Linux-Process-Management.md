# Linux Process Management & Troubleshooting

## Scenario

An Apache HTTP server is expected to be available on:

```text
TCP port 6400
```

on multiple application servers.

On `stapp01`, the web service is unavailable.

The goal is to troubleshoot the problem systematically, identify the process preventing Apache from starting, resolve the conflict, and verify that Apache is serving traffic correctly.

---

## 1. Check Apache Service Status

Start by checking whether the service is running:

```bash
sudo systemctl status httpd
```

In this scenario, Apache was:

```text
inactive / failed
```

This tells us that the service itself is not currently available.

---

## 2. Attempt to Start Apache

Because the service is stopped, try starting it:

```bash
sudo systemctl start httpd
```

If the service starts successfully, continue with connectivity checks.

If it fails, inspect the logs.

---

## 3. Inspect Service Logs

Use `journalctl` to investigate why Apache failed:

```bash
sudo journalctl -u httpd -n 50
```

Useful alternatives:

```bash
sudo journalctl -xeu httpd
```

The important error in this case indicated that:

```text
port 6400 is already in use
```

This means another process is already bound to the port Apache is configured to use.

---

# Understanding Port Conflicts

Only one process can normally bind to the same IP address and TCP port combination at a time.

If Apache attempts to listen on:

```text
0.0.0.0:6400
```

but another process already owns that socket, Apache cannot start.

The next step is therefore to identify the process using the port.

---

## 4. Find the Process Using Port 6400

Use `ss`:

```bash
sudo ss -lntp | grep 6400
```

### Command Breakdown

```text
ss      → display socket information
-l      → show listening sockets
-n      → show numeric ports/IPs
-t      → show TCP sockets
-p      → show process information
```

Example output may look like:

```text
LISTEN 0 128 0.0.0.0:6400 0.0.0.0:* users:(("sendmail",pid=29914,fd=4))
```

This reveals:

```text
Process: sendmail
PID:     29914
Port:    6400
```

Another useful command is:

```bash
sudo lsof -i :6400
```

`lsof` means **list open files**, and because Linux treats sockets as file descriptors, it can show which process owns a network port.

---

## 5. Inspect the Process

Once the PID is known:

```bash
ps -fp 29914
```

### Command Breakdown

```text
ps      → display processes
-f      → full-format output
-p      → select a specific PID
```

This helps confirm:

- process name
- owner
- parent PID
- startup command
- process arguments

In this scenario, the unexpected process was:

```text
sendmail
```

---

# 6. Free the Port

If the process is not supposed to use the Apache port, stop it.

First, try a normal termination:

```bash
sudo kill 29914
```

By default, `kill` sends:

```text
SIGTERM
```

which asks the process to shut down gracefully.

Verify whether the port is now free:

```bash
sudo ss -lntp | grep 6400
```

If there is no output, nothing is listening on the port.

---

## If the Process Is Managed by systemd

Killing a process directly may not be enough if systemd automatically restarts it.

Check:

```bash
systemctl status sendmail
```

If it is running as a managed service, stop it cleanly:

```bash
sudo systemctl stop sendmail
```

Then verify port `6400` again.

---

## 7. Start Apache

Once the conflicting process is gone:

```bash
sudo systemctl start httpd
```

Check:

```bash
sudo systemctl status httpd
```

You want:

```text
active (running)
```

---

## 8. Verify Apache Owns Port 6400

Run:

```bash
sudo ss -lntp | grep 6400
```

You should now see Apache/httpd listening on the port.

Example:

```text
LISTEN ... 0.0.0.0:6400 ... httpd
```

---

## 9. Test the Web Service

Test locally:

```bash
curl http://localhost:6400
```

Then, if required, test from another server:

```bash
curl http://stapp01:6400
```

A successful HTTP response confirms that:

```text
Apache running        ✅
Port 6400 listening   ✅
Network path working  ✅
Application reachable ✅
```

---

# Useful Process Troubleshooting Commands

## List Processes

```bash
ps aux
```

Shows running processes with CPU and memory information.

---

## Find a Process by Name

```bash
pgrep -a httpd
```

or:

```bash
ps aux | grep httpd
```

---

## Inspect One PID

```bash
ps -fp <PID>
```

---

## Live Process Monitoring

```bash
top
```

or:

```bash
top -p <PID>
```

---

## Find Which Process Uses a Port

```bash
sudo ss -lntp
```

Specific port:

```bash
sudo ss -lntp | grep 6400
```

Alternative:

```bash
sudo lsof -i :6400
```

---

# Understanding `kill`

The basic form is:

```bash
kill <PID>
```

This sends `SIGTERM`.

Example:

```bash
sudo kill 29914
```

If the process refuses to stop, a stronger signal exists:

```bash
sudo kill -9 29914
```

`-9` sends:

```text
SIGKILL
```

This forces immediate termination.

Use `kill -9` only as a last resort because the process cannot perform cleanup.

---

# Troubleshooting Thought Process

A strong process-troubleshooting workflow is:

```text
Service unavailable
        ↓
Check systemctl status
        ↓
Try to start service
        ↓
Start fails
        ↓
Read journal logs
        ↓
Port already in use
        ↓
Find process with ss/lsof
        ↓
Inspect PID
        ↓
Stop conflicting process
        ↓
Verify port is free
        ↓
Start Apache
        ↓
Verify port
        ↓
Test with curl
```

The important principle is:

```text
Observe → Form hypothesis → Test → Fix → Verify
```

Avoid blindly restarting or killing processes before understanding the cause.

---

# Key Takeaways

1. `systemctl status` tells you whether the service is running.
2. `journalctl` explains why a service failed.
3. `ss -lntp` reveals which process owns a TCP listening port.
4. `ps -fp <PID>` provides details about a specific process.
5. `kill <PID>` gracefully requests process termination.
6. Port conflicts can prevent services from starting.
7. Always verify the fix with both socket checks and an application-level test such as `curl`.

---

## Final Command Summary

```bash
sudo systemctl status httpd

sudo systemctl start httpd

sudo journalctl -u httpd -n 50

sudo ss -lntp | grep 6400

ps -fp <PID>

sudo kill <PID>

sudo ss -lntp | grep 6400

sudo systemctl start httpd

sudo systemctl status httpd

sudo ss -lntp | grep 6400

curl http://localhost:6400
```

This workflow provides a clean and repeatable method for troubleshooting Linux services that fail because of conflicting processes or occupied network ports.
