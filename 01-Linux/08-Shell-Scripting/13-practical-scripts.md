# 🛠️ Practical Bash Scripts

> Small real-world examples for Linux administration and DevOps tasks.

---

## On This Page

- [Quick Cheat Sheet](#quick-cheat-sheet)
- [Disk Usage Check](#disk-usage-check)
- [Service Health Check](#service-health-check)
- [Port Check](#port-check)
- [Backup Script](#backup-script)
- [Log Cleanup](#log-cleanup)
- [HTTP Endpoint Check](#http-endpoint-check)
- [Conclusion](#conclusion)

---

## Quick Cheat Sheet

| Task | Useful Commands |
|---|---|
| Disk usage | `df`, `awk` |
| Service check | `systemctl` |
| Port check | `nc`, `ss` |
| Backup | `tar` |
| Log cleanup | `find` |
| HTTP check | `curl` |

---

## Disk Usage Check

```bash
#!/bin/bash

USAGE=$(df / | awk 'NR==2 {print $5}' | tr -d '%')

if [ "$USAGE" -ge 80 ]; then
    echo "WARNING: Disk usage is ${USAGE}%"
else
    echo "Disk usage OK: ${USAGE}%"
fi
```

---

## Service Health Check

```bash
#!/bin/bash

SERVICE="$1"

if systemctl is-active --quiet "$SERVICE"; then
    echo "$SERVICE is running"
else
    echo "$SERVICE is down"
    exit 1
fi
```

Run:

```bash
./check-service.sh nginx
```

---

## Port Check

```bash
#!/bin/bash

HOST="$1"
PORT="$2"

if nc -z "$HOST" "$PORT"; then
    echo "$HOST:$PORT is reachable"
else
    echo "$HOST:$PORT is unreachable"
fi
```

---

## Backup Script

```bash
#!/bin/bash

SOURCE="/etc"
DEST="/backup"
DATE=$(date +%F)

tar -czf "${DEST}/etc-${DATE}.tar.gz" "$SOURCE"
```

---

## Log Cleanup

Delete `.log` files older than 30 days:

```bash
#!/bin/bash

find /var/log/myapp \
    -type f \
    -name "*.log" \
    -mtime +30 \
    -delete
```

Be very careful with automated deletion.

Test first:

```bash
find /var/log/myapp -type f -name "*.log" -mtime +30
```

---

## HTTP Endpoint Check

```bash
#!/bin/bash

URL="$1"

if curl -fsS "$URL" > /dev/null; then
    echo "$URL is healthy"
else
    echo "$URL check failed"
    exit 1
fi
```

---

## Simple Multi-Server Check

```bash
#!/bin/bash

SERVERS=("web01" "web02" "db01")

for SERVER in "${SERVERS[@]}"; do
    if ping -c 1 "$SERVER" > /dev/null 2>&1; then
        echo "$SERVER: UP"
    else
        echo "$SERVER: DOWN"
    fi
done
```

---

## Good Script Habits

Prefer:

```bash
set -euo pipefail
```

when appropriate.

Also:

```text
Quote variables
Validate arguments
Check exit codes
Avoid hard-coded secrets
Log important actions
Test destructive commands first
```

---

## Conclusion

The real value of Bash is combining simple Linux commands into repeatable workflows.

Typical pattern:

```text
Input
  ↓
Command
  ↓
Check result
  ↓
Take action
  ↓
Exit status
```

That is the foundation of practical Linux automation.