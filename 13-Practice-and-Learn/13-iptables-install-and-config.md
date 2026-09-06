# Linux Firewall with iptables

## Scenario

A web application is running on several Linux application servers.

Apache is listening on:

```text
TCP port 8084
```

The security requirement is:

- Install `iptables` on all application servers.
- Allow access to port `8084` only from the Load Balancer host.
- Reject access to port `8084` from all other hosts.
- Make the rules persistent across reboots.

Example Load Balancer:

```text
Hostname: stlb01
IP: 10.244.97.132
```

---

# 1. Install iptables

On CentOS/RHEL-style systems:

```bash
sudo dnf install -y iptables iptables-services
```

Depending on the distribution/version, the installed package may internally use the legacy iptables backend.

Enable the iptables service:

```bash
sudo systemctl enable iptables
```

Check:

```bash
sudo systemctl status iptables
```

---

# 2. Find the Load Balancer IP

Resolve the Load Balancer hostname:

```bash
nslookup stlb01
```

Example:

```text
Name:    stlb01
Address: 10.244.97.132
```

This IP will be used as the trusted source.

---

# 3. Inspect Existing Rules

Before modifying firewall rules, always inspect the current ruleset:

```bash
sudo iptables -L INPUT -n -v --line-numbers
```

Example:

```text
Chain INPUT (policy ACCEPT)

num  target  prot  source         destination
1    ACCEPT  all   0.0.0.0/0     0.0.0.0/0
2    ACCEPT  tcp   0.0.0.0/0     0.0.0.0/0  tcp dpt:22
3    REJECT  all   0.0.0.0/0     0.0.0.0/0
```

Rule order is extremely important.

iptables evaluates rules from **top to bottom** and stops at the first matching rule.

---

# 4. Allow the Load Balancer

If the INPUT chain is empty:

```bash
sudo iptables -A INPUT \
  -p tcp \
  -s 10.244.97.132 \
  --dport 8084 \
  -j ACCEPT
```

This means:

```text
If traffic:
- enters the INPUT chain
- uses TCP
- comes from 10.244.97.132
- targets port 8084

then ACCEPT it.
```

---

# 5. Reject Everyone Else

Add a second rule:

```bash
sudo iptables -A INPUT \
  -p tcp \
  --dport 8084 \
  -j REJECT
```

This matches any remaining TCP connection to port `8084`.

Because the Load Balancer rule comes first, it has already been accepted.

Everyone else reaches the second rule and is rejected.

The order must therefore be:

```text
1. ACCEPT trusted Load Balancer
2. REJECT everyone else
```

---

# 6. Understanding Important iptables Arguments

## `-A`

```bash
-A INPUT
```

means:

> Append a rule to the end of the INPUT chain.

Example:

```bash
sudo iptables -A INPUT -p tcp --dport 8084 -j REJECT
```

---

## `-I`

```bash
-I INPUT 5
```

means:

> Insert a rule at a specific position.

Example:

```bash
sudo iptables -I INPUT 5 \
  -p tcp \
  -s 10.244.97.132 \
  --dport 8084 \
  -j ACCEPT
```

This is important when an existing catch-all `REJECT` rule appears earlier in the chain.

---

## `-L`

```bash
iptables -L
```

means:

> List firewall rules.

To inspect only the INPUT chain:

```bash
sudo iptables -L INPUT
```

---

## `-n`

```bash
-n
```

means:

> Use numeric output.

Without `-n`, iptables may try to resolve IP addresses and port numbers into hostnames and service names.

Numeric mode is faster and clearer for troubleshooting.

Example:

```text
10.244.97.132
```

instead of a resolved hostname.

---

## `-v`

```bash
-v
```

means:

> Verbose output.

It displays extra information such as:

```text
packet counters
byte counters
interfaces
protocol information
```

Example:

```bash
sudo iptables -L INPUT -n -v
```

---

## `-j`

```bash
-j ACCEPT
```

means:

> Jump to a target/action.

Common targets include:

```text
ACCEPT
DROP
REJECT
```

### ACCEPT

```bash
-j ACCEPT
```

Allow the packet.

### DROP

```bash
-j DROP
```

Silently discard the packet.

The client usually experiences a timeout.

### REJECT

```bash
-j REJECT
```

Block the packet and send an error response.

This gives the client immediate feedback.

---

## `-p`

```bash
-p tcp
```

specifies the protocol.

Common values:

```text
tcp
udp
icmp
```

---

## `-s`

```bash
-s 10.244.97.132
```

means:

> Match packets from this source IP.

---

## `--dport`

```bash
--dport 8084
```

means:

> Match packets whose destination port is 8084.

Because `--dport` is a TCP/UDP option, it is normally used together with:

```bash
-p tcp
```

or:

```bash
-p udp
```

---

# 7. Existing Catch-All Rules

Suppose the server already has:

```text
5  REJECT all
```

If you simply append:

```bash
sudo iptables -A INPUT \
  -p tcp \
  -s 10.244.97.132 \
  --dport 8084 \
  -j ACCEPT
```

the new rule might become rule 6.

That will not work.

Traffic reaches rule 5 first:

```text
REJECT all
```

and never reaches rule 6.

Instead, insert the trusted rule before the catch-all rule:

```bash
sudo iptables -I INPUT 5 \
  -p tcp \
  -s 10.244.97.132 \
  --dport 8084 \
  -j ACCEPT
```

Then insert the specific port rejection immediately after:

```bash
sudo iptables -I INPUT 6 \
  -p tcp \
  --dport 8084 \
  -j REJECT
```

Desired order:

```text
5 ACCEPT tcp source=10.244.97.132 dpt:8084
6 REJECT tcp source=0.0.0.0/0    dpt:8084
7 REJECT all
```

---

# 8. Delete an Incorrect Rule

First display line numbers:

```bash
sudo iptables -L INPUT -n -v --line-numbers
```

If an incorrect rule is line 8:

```bash
sudo iptables -D INPUT 8
```

Where:

```text
-D = delete
```

Always display the rules again after deleting because rule numbers change.

---

# 9. Save Rules Persistently

iptables rules normally exist in memory.

Without persistence, they may disappear after reboot.

Save the current rules:

```bash
sudo sh -c 'iptables-save > /etc/sysconfig/iptables'
```

Why use `sh -c`?

This command does **not** work as expected:

```bash
sudo iptables-save > /etc/sysconfig/iptables
```

because `sudo` applies only to:

```text
iptables-save
```

The shell performs:

```text
>
```

as the normal user, which may produce:

```text
Permission denied
```

Using:

```bash
sudo sh -c 'iptables-save > /etc/sysconfig/iptables'
```

causes both the command and the redirection to run as root.

Verify:

```bash
sudo cat /etc/sysconfig/iptables
```

---

# 10. Enable Rules at Boot

Ensure the service is enabled:

```bash
sudo systemctl enable iptables
```

Verify:

```bash
sudo systemctl is-enabled iptables
```

Expected:

```text
enabled
```

Check status:

```bash
sudo systemctl status iptables
```

You may see:

```text
active (exited)
```

This is normal.

The service loads the rules and then exits successfully.

---

# 11. Final Verification

Inspect the active rules:

```bash
sudo iptables -L INPUT -n -v --line-numbers
```

You should see:

```text
ACCEPT tcp source=10.244.97.132 dpt:8084
REJECT tcp source=0.0.0.0/0    dpt:8084
```

Verify the saved rules:

```bash
sudo grep 8084 /etc/sysconfig/iptables
```

Example:

```text
-A INPUT -s 10.244.97.132/32 -p tcp --dport 8084 -j ACCEPT
-A INPUT -p tcp --dport 8084 -j REJECT
```

---

# Troubleshooting Notes

## Rule Exists but Does Not Work

Check the order:

```bash
sudo iptables -L INPUT -n -v --line-numbers
```

A broad `DROP` or `REJECT` rule may appear before your rule.

Remember:

```text
iptables processes rules from top to bottom.
```

---

## `service: command not found`

Some newer Linux systems may not have the old:

```bash
service
```

wrapper installed.

Use:

```bash
systemctl
```

instead.

---

## Permission Denied When Saving Rules

Incorrect:

```bash
sudo iptables-save > /etc/sysconfig/iptables
```

Correct:

```bash
sudo sh -c 'iptables-save > /etc/sysconfig/iptables'
```

---

# Useful Cheat Sheet

```bash
# List rules
sudo iptables -L

# List INPUT rules
sudo iptables -L INPUT

# Numeric output
sudo iptables -L INPUT -n

# Verbose output
sudo iptables -L INPUT -n -v

# Include line numbers
sudo iptables -L INPUT -n -v --line-numbers

# Append rule
sudo iptables -A INPUT ...

# Insert rule
sudo iptables -I INPUT <position> ...

# Delete rule
sudo iptables -D INPUT <line-number>

# Allow TCP port
sudo iptables -A INPUT -p tcp --dport 8084 -j ACCEPT

# Reject TCP port
sudo iptables -A INPUT -p tcp --dport 8084 -j REJECT

# Save rules
sudo sh -c 'iptables-save > /etc/sysconfig/iptables'
```

---

# Key Takeaways

iptables is fundamentally a **rule-processing engine**.

The most important concepts are:

```text
Chain
  ↓
Rules processed top-to-bottom
  ↓
Match conditions
  ↓
Target/action
```

For this scenario:

```text
Incoming connection to TCP/8084
              |
              v
Is source the Load Balancer?
        /             \
      Yes             No
       |               |
    ACCEPT           REJECT
```

The three most important lessons from this exercise are:

1. **Rule order matters.**
2. **Use the smallest possible firewall change.**
3. **Save the rules if they must survive a reboot.**

A firewall configuration can be syntactically valid yet ineffective if an earlier rule matches the traffic first. Always inspect the complete chain before adding new rules.