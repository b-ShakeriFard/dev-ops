# 🛠️ Process & systemd Troubleshooting

> Diagnosing failed services, hung processes, high resource usage, and restart loops.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Service Failed](#service-failed)
- [High CPU or Memory](#high-cpu-or-memory)
- [Hung Process](#hung-process)
- [Restart Loops](#restart-loops)
- [Missing Logs](#missing-logs)
- [Troubleshooting Workflow](#troubleshooting-workflow)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl status SERVICE` | Check service state |
| `journalctl -u SERVICE` | Show service logs |
| `systemctl --failed` | List failed units |
| `ps aux` | Inspect processes |
| `top` | Monitor CPU and memory |
| `pgrep -a NAME` | Find process by name |
| `kill PID` | Send SIGTERM |
| `kill -9 PID` | Force termination |
| `systemctl restart SERVICE` | Restart service |

---

## Service Failed

Check:

```bash
systemctl status nginx
```

Then inspect logs:

```bash
journalctl -u nginx -n 50
```

Common causes:

- Bad configuration
- Missing file
- Permission problem
- Port already in use
- Dependency failure

---

## High CPU or Memory

Start with:

```bash
top
```

Or:

```bash
ps -eo pid,user,%cpu,%mem,cmd \
--sort=-%cpu
```

For memory:

```bash
ps -eo pid,user,%mem,cmd \
--sort=-%mem
```

Identify the process before restarting or killing it.

---

## Hung Process

Find PID:

```bash
pgrep -a myapp
```

Try graceful termination:

```bash
kill PID
```

If it does not stop:

```bash
kill -9 PID
```

Use `SIGKILL` only as a last resort.

---

## Restart Loops

A service may repeatedly start and fail.

Check:

```bash
systemctl status SERVICE
```

Then:

```bash
journalctl -u SERVICE -f
```

Inspect unit settings:

```bash
systemctl cat SERVICE
```

Look for restart directives such as:

```text
Restart=always
```

---

## Missing Logs

Check the correct unit name:

```bash
systemctl list-units --type=service
```

Then:

```bash
journalctl -u SERVICE
```

You can also inspect current boot errors:

```bash
journalctl -b -p err
```

---

## Unit File Changed but Service Did Not

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Restart the service:

```bash
sudo systemctl restart SERVICE
```

---

## Port Already in Use

Check:

```bash
ss -tulpn
```

Example:

```bash
ss -tulpn | grep :8080
```

Find the owning process and resolve the conflict.

---

## Troubleshooting Workflow

```text
Service Problem
      ↓
systemctl status
      ↓
journalctl
      ↓
ps / top
      ↓
Check ports / files / permissions
      ↓
Restart or signal process
      ↓
Verify
```

---

## Related Topics

- `processes.md`
- `ps.md`
- `top.md`
- `kill.md`
- `systemctl.md`
- `journalctl.md`

---

## Conclusion

Most process and service problems can be narrowed down with:

```bash
systemctl status SERVICE
journalctl -u SERVICE
top
ps aux
ss -tulpn
```

The key is to identify the cause before restarting or killing processes.