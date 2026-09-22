# ⚡ Practical Linux CLI Cheatsheet

> Common command combinations for everyday Linux administration and troubleshooting.

---

## On This Page

- [Navigation](#navigation)
- [Files & Directories](#files--directories)
- [Viewing Files](#viewing-files)
- [Searching](#searching)
- [Text Processing](#text-processing)
- [System Information](#system-information)
- [Environment](#environment)
- [Processes & Services](#processes--services)
- [Logs](#logs)
- [Networking](#networking)
- [Downloads](#downloads)

---

## Navigation

```bash
pwd
ls -lah
cd /var/log
cd ..
cd -
```

Useful combination:

```bash
pwd && ls -lah
```

---

## Files & Directories

Create:

```bash
touch file.txt
mkdir -p project/{config,logs,data}
```

Copy:

```bash
cp file.txt file.txt.bak
cp -a source/. destination/
```

Move / rename:

```bash
mv old.txt new.txt
```

Remove:

```bash
rm -i file.txt
rm -r directory/
```

Check before destructive actions:

```bash
pwd
ls -lah
```

---

## Viewing Files

Short file:

```bash
cat /etc/hosts
```

Large file:

```bash
less /var/log/messages
```

Beginning / end:

```bash
head -20 file.txt
tail -50 file.txt
```

Follow log:

```bash
tail -F application.log
```

---

## Searching

Search text:

```bash
grep -i "error" application.log
```

Search recursively:

```bash
grep -R "PORT" /etc/myapp/
```

Find files:

```bash
find /var/log -type f -name "*.log"
```

Find large files:

```bash
find /var -type f -size +1G
```

---

## Text Processing

Extract usernames:

```bash
cut -d: -f1 /etc/passwd
```

Count shells:

```bash
cut -d: -f7 /etc/passwd |
sort |
uniq -c |
sort -nr
```

Structured output:

```bash
ps aux | awk '{print $1, $2, $3, $11}'
```

Replace text:

```bash
sed 's/old/new/g' file.txt
```

---

## System Information

```bash
whoami
id
hostname
uname -r
uname -m
date
uptime
```

Quick server summary:

```bash
printf "Host: %s\nUser: %s\nKernel: %s\n" \
"$(hostname)" "$(whoami)" "$(uname -r)"
```

---

## Environment

Show environment:

```bash
env | sort
```

Check PATH:

```bash
echo "$PATH" | tr ':' '\n'
```

Find command:

```bash
command -v python3
```

Export variable:

```bash
export APP_ENV="production"
```

Reload Bash config:

```bash
source ~/.bashrc
```

---

## Processes & Services

Top CPU processes:

```bash
ps aux --sort=-%cpu | head
```

Top memory processes:

```bash
ps aux --sort=-%mem | head
```

Find process:

```bash
pgrep -a nginx
```

Check service:

```bash
systemctl status nginx
```

Failed services:

```bash
systemctl --failed
```

---

## Logs

Recent service logs:

```bash
journalctl -u nginx -n 50
```

Follow service logs:

```bash
journalctl -u nginx -f
```

Current boot errors:

```bash
journalctl -b -p err
```

Search traditional logs:

```bash
grep -i error /var/log/messages
```

---

## Networking

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

Check port:

```bash
nc -zv server01 443
```

HTTP health check:

```bash
curl -fsS http://localhost:8080/health
```

---

## Downloads

Download with curl:

```bash
curl -LO https://example.com/file.tar.gz
```

Inspect headers:

```bash
curl -I https://example.com
```

Download with wget:

```bash
wget https://example.com/file.iso
```

Resume interrupted download:

```bash
wget -c https://example.com/file.iso
```

---

## Useful Pipeline Mental Model

```text
Command
   ↓
Filter
   ↓
Transform
   ↓
Sort
   ↓
Save / Act
```

Example:

```bash
ps aux |
awk '{print $1}' |
sort |
uniq -c |
sort -nr
```

---

## Final Rule

Before running a destructive command:

```bash
pwd
ls -lah
```

Before fixing a problem:

```text
Observe
   ↓
Verify
   ↓
Change
   ↓
Verify again
```

This cheatsheet is meant to be a quick operational reference, not a replacement for `man` pages.