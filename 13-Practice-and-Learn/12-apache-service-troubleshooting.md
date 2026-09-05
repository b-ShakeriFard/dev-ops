# Apache Service Troubleshooting on a Linux App Server

## Overview

This lab focused on troubleshooting an Apache web service that was not reachable from the jump host on the required port.

The environment contained three application servers:

- `stapp01`
- `stapp02`
- `stapp03`

The expected Apache port in this challenge was `8082`.

The first goal was to identify which application server was failing, and then determine whether the issue was caused by Apache itself, a port conflict, or firewall filtering.

---

## 1. Identify the Faulty Server

From the jump host, test all three servers:

```bash
curl http://stapp01:8082
curl http://stapp02:8082
curl http://stapp03:8082
```

`stapp01` failed while the other servers responded correctly.

This narrowed the investigation to App Server 1.

---

## 2. Check Which Process Owns the Port

After connecting to `stapp01`, check port `8082`:

```bash
sudo netstat -tulpn | grep 8082
```

The output showed that `sendmail` was listening on:

```text
127.0.0.1:8082
```

This immediately indicated a port conflict because Apache was also expected to use port `8082`.

---

## 3. Confirm Apache Failure

Check Apache:

```bash
sudo systemctl status httpd
```

The service was in a failed state and reported errors similar to:

```text
Address already in use
no listening sockets available
```

This confirmed that Apache could not bind to its configured port because Sendmail had already claimed it.

---

## 4. Locate the Incorrect Sendmail Configuration

Search the Sendmail configuration for the conflicting port:

```bash
sudo grep -R "8082" /etc/mail/
```

The important line was located in:

```text
/etc/mail/sendmail.mc
```

It contained:

```text
DAEMON_OPTIONS(`Port=8082,Addr=127.0.0.1, Name=MTA')dnl
```

Sendmail was incorrectly configured to use Apache's port.

---

## 5. Correct the Port Conflict

Edit the configuration:

```bash
sudo vi /etc/mail/sendmail.mc
```

Change:

```text
Port=8082
```

to:

```text
Port=smtp
```

Then rebuild the Sendmail configuration and restart the services:

```bash
sudo make -C /etc/mail
sudo systemctl restart sendmail
sudo systemctl restart httpd
```

Verify that Apache now owns the port:

```bash
sudo netstat -tulpn | grep 8082
```

Expected result:

```text
:::8082    LISTEN    httpd
```

---

## 6. Verify Firewall Rules

Even with Apache running, the service must still be reachable remotely.

Check the firewall:

```bash
sudo iptables -L INPUT -n --line-numbers
```

If port `8082` is blocked by a later `REJECT` rule, insert an allow rule before it:

```bash
sudo iptables -I INPUT 4 -p tcp --dport 8082 -j ACCEPT
```

---

## Final Validation

From the jump host:

```bash
curl http://stapp01:8082
```

If the page loads successfully, the issue is resolved.

---

## Key Takeaway

This challenge demonstrated an important troubleshooting sequence:

```text
Connectivity → Port Ownership → Service Status → Configuration → Firewall
```

The root cause was a **port conflict between Sendmail and Apache**, with firewall rules also needing verification to ensure remote access.
