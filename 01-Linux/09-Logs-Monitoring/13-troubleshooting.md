# 🛠️ Logs & Monitoring Troubleshooting

> A practical workflow for diagnosing Linux performance and service problems.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Start with the Symptom](#start-with-the-symptom)
- [Check Logs](#check-logs)
- [Check Resources](#check-resources)
- [Check Processes](#check-processes)
- [Check Network](#check-network)
- [Check Kernel Messages](#check-kernel-messages)
- [Troubleshooting Workflow](#troubleshooting-workflow)

---

## Quick Cheat Sheet

| Command | Purpose |
|---|---|
| `systemctl --failed` | Show failed services |
| `journalctl -p err -b` | Current boot errors |
| `top` | CPU/process view |
| `free -h` | Memory usage |
| `df -h` | Filesystem usage |
| `df -i` | Inode usage |
| `ss -tulpn` | Listening ports |
| `uptime` | Load average |
| `dmesg -T` | Kernel messages |

---

## Start with the Symptom

Avoid checking everything randomly.

First ask:

```text
What exactly is wrong?
```

Examples:

```text
Service will not start
Server is slow
Disk is full
Application is unreachable
CPU is high
Memory is exhausted
```

Then investigate the relevant layer.

---

## Check Logs

Failed service:

```bash
systemctl status nginx
```

Service logs:

```bash
journalctl -u nginx -n 50
```

Current boot errors:

```bash
journalctl -b -p err
```

Traditional logs:

```bash
tail -f /var/log/messages
```

or:

```bash
tail -f /var/log/syslog
```

---

## Check Resources

CPU and processes:

```bash
top
```

Memory:

```bash
free -h
```

Disk:

```bash
df -h
df -i
```

Load:

```bash
uptime
```

If load is high, compare with:

```bash
nproc
vmstat 1
```

---

## Check Processes

Find resource-heavy processes:

```bash
ps aux --sort=-%cpu | head
```

Memory:

```bash
ps aux --sort=-%mem | head
```

Inspect specific process:

```bash
ps -p PID -o pid,ppid,user,stat,%cpu,%mem,cmd
```

---

## Check Network

Interfaces:

```bash
ip addr
```

Routes:

```bash
ip route
```

Listening ports:

```bash
ss -tulpn
```

Application test:

```bash
curl -v http://server01:8080
```

---

## Check Kernel Messages

For hardware, drivers, disks, or networking:

```bash
dmesg -T | tail -50
```

or:

```bash
journalctl -k -p err
```

---

## Troubleshooting Workflow

```text
Identify symptom
      ↓
Check service status
      ↓
Check logs
      ↓
Check CPU / memory / disk
      ↓
Check processes
      ↓
Check network
      ↓
Check kernel
      ↓
Fix root cause
      ↓
Verify
```

---

## Important Rule

Do not immediately:

```text
Restart everything
Kill processes
Delete files
Disable services
```

First collect enough evidence to understand the cause.

---

## Related Topics

- `journalctl.md`
- `cpu-memory.md`
- `disk-monitoring.md`
- `process-monitoring.md`
- `network-monitoring.md`
- `uptime-load.md`

---

## Conclusion

A strong Linux troubleshooting habit is:

```text
Observe
   ↓
Measure
   ↓
Correlate logs and metrics
   ↓
Identify root cause
   ↓
Fix
   ↓
Verify
```

Good troubleshooting is systematic, not random.