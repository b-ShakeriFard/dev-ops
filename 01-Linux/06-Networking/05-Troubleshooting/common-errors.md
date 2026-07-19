# 🚨 Common Linux Network Errors

> A quick reference for identifying common network problems and choosing the right troubleshooting command.

---

## 🎯 Troubleshooting Principle

Start with the symptom, then work downward:

```text
Application
    ↓
Port
    ↓
DNS
    ↓
Routing
    ↓
IP Address
    ↓
Interface
```

Avoid changing configuration before identifying the failed layer.

---

# ❌ Network Is Unreachable

Example:

```text
connect: Network is unreachable
```

Likely causes:

- Missing default route
- Wrong gateway
- Interface down
- Destination network has no route

Check:

```bash
ip link
ip addr
ip route
ip route get <destination-ip>
```

Example:

```bash
ip route get 8.8.8.8
```

---

# ❌ No Route to Host

Example:

```text
No route to host
```

Likely causes:

- Missing route
- Incorrect gateway
- Firewall rejection
- Remote network unavailable

Check:

```bash
ip route
ping -c 4 <gateway-ip>
traceroute <destination>
firewall-cmd --list-all
```

---

# ❌ Connection Refused

Example:

```text
curl: Connection refused
```

Likely meaning:

```text
The host is reachable,
but nothing accepts the connection.
```

Possible causes:

- Service stopped
- Wrong port
- Application bound to another address

Check:

```bash
systemctl status <service>
sudo ss -tulpn
curl http://localhost:<port>
```

Example:

```bash
sudo ss -tulpn | grep 8080
```

---

# ⏳ Connection Timed Out

Example:

```text
curl: Connection timed out
```

Likely causes:

- Firewall silently drops traffic
- Routing problem
- Remote host unavailable
- Return path failure

Check:

```bash
ping <host>
ip route get <host>
firewall-cmd --list-all
nc -zv <host> <port>
sudo tcpdump -nn -i any host <host>
```

---

# 🌍 Temporary Failure in Name Resolution

Example:

```text
Temporary failure in name resolution
```

Likely causes:

- Missing DNS server
- Incorrect `/etc/resolv.conf`
- DNS server unavailable
- NetworkManager DNS misconfiguration

Check:

```bash
cat /etc/resolv.conf
nmcli device show
dig example.com
dig @<dns-server> example.com
```

---

# 🌍 Name or Service Not Known

Example:

```text
Name or service not known
```

Likely causes:

- Invalid hostname
- Missing DNS record
- Typo
- Broken search domain

Check:

```bash
dig <hostname>
getent hosts <hostname>
cat /etc/hosts
hostnamectl
```

---

# 🌍 NXDOMAIN

Example:

```text
status: NXDOMAIN
```

Meaning:

```text
The DNS server says
the requested name does not exist.
```

Check:

```bash
dig <hostname>
dig @<dns-server> <hostname>
```

Possible fixes:

- Create the DNS record
- Correct the hostname
- Query the correct DNS server

---

# 🌍 SERVFAIL

Example:

```text
status: SERVFAIL
```

Likely causes:

- DNS server failure
- Broken DNS forwarding
- DNSSEC problem
- Upstream DNS unavailable

Check:

```bash
dig <hostname>
dig @<dns-server> <hostname>
dig +trace <hostname>
```

---

# 📡 Destination Host Unreachable

Example:

```text
Destination Host Unreachable
```

Likely causes:

- Gateway cannot reach destination
- ARP resolution failure
- Wrong subnet
- Remote host offline

Check:

```bash
ip addr
ip route
ip neigh
ping <gateway-ip>
```

---

# 📡 100% Packet Loss

Example:

```text
4 packets transmitted
0 received
100% packet loss
```

Possible causes:

- Host unavailable
- ICMP blocked
- Wrong route
- Firewall
- Network failure

Check:

```bash
ip route get <destination>
traceroute <destination>
nc -zv <destination> <port>
```

> Failed ping does not always mean the host is offline. ICMP may be blocked.

---

# 🔌 Interface Is DOWN

Example:

```text
ens33: DOWN
```

Check:

```bash
ip link
nmcli device status
```

Enable temporarily:

```bash
sudo ip link set ens33 up
```

Activate through NetworkManager:

```bash
nmcli connection up <connection-name>
```

---

# 🔌 Device Is Disconnected

Example:

```text
ens33 ethernet disconnected
```

Check:

```bash
nmcli device status
nmcli connection show
```

Activate:

```bash
nmcli connection up <connection-name>
```

Possible causes:

- No active connection profile
- Virtual NIC disconnected
- Cable or switch problem

---

# ⚠️ Device Is Unmanaged

Example:

```text
ens33 ethernet unmanaged
```

Meaning:

```text
NetworkManager is not controlling the interface.
```

Check:

```bash
nmcli device status
systemctl status NetworkManager
```

Inspect:

```text
/etc/NetworkManager/
```

and distribution-specific network configuration.

---

# 📍 Duplicate IP Address

Symptoms:

- Intermittent connectivity
- ARP entries change unexpectedly
- SSH connects to the wrong host
- Network repeatedly disconnects

Check:

```bash
ip addr
ip neigh
arping -D -I ens33 <ip-address>
```

Example:

```bash
sudo arping -D -I ens33 192.168.1.50
```

---

# 🧭 Local Network Works, Internet Fails

Likely cause:

```text
Missing or incorrect default gateway
```

Check:

```bash
ip route
```

Expected:

```text
default via 192.168.1.1 dev ens33
```

Test:

```bash
ping -c 4 192.168.1.1
ping -c 4 8.8.8.8
```

---

# 🌍 IP Works, Hostname Fails

Example:

```bash
ping 8.8.8.8
```

works, but:

```bash
ping google.com
```

fails.

Conclusion:

```text
Basic connectivity works.
DNS is failing.
```

Check:

```bash
cat /etc/resolv.conf
dig google.com
nmcli device show
```

---

# 🚪 Port Is Open but Application Fails

Firewall check:

```bash
firewall-cmd --query-port=8080/tcp
```

returns:

```text
yes
```

But the application still fails.

Check:

```bash
sudo ss -tulpn | grep 8080
curl -v http://localhost:8080
systemctl status <service>
```

The firewall may allow the port while no application is listening.

---

# 🔥 Service Works Locally but Not Remotely

Example:

```bash
curl http://localhost:8080
```

works, but remote access fails.

Possible causes:

- Firewall
- Application bound only to localhost
- Wrong zone
- Routing problem

Check:

```bash
sudo ss -tulpn | grep 8080
firewall-cmd --get-active-zones
firewall-cmd --list-all
```

Problematic binding:

```text
127.0.0.1:8080
```

Remote-access binding:

```text
0.0.0.0:8080
```

or:

```text
<server-ip>:8080
```

---

# 🔥 Firewall Rule Disappeared After Reboot

Cause:

```text
The rule existed only in runtime configuration.
```

Compare:

```bash
firewall-cmd --list-all
```

with:

```bash
firewall-cmd --list-all --permanent
```

Save runtime rules:

```bash
sudo firewall-cmd --runtime-to-permanent
```

or add the rule again using:

```bash
--permanent
```

---

# 🔥 Rule Exists but Traffic Is Still Blocked

Likely cause:

```text
The rule is in the wrong zone.
```

Check:

```bash
firewall-cmd --get-active-zones
firewall-cmd --get-zone-of-interface=ens33
firewall-cmd --zone=public --list-all
```

---

# 🔐 SSL Certificate Problem

Example:

```text
SSL certificate problem
```

Possible causes:

- Self-signed certificate
- Expired certificate
- Hostname mismatch
- Missing CA certificate

Inspect:

```bash
curl -v https://example.com
openssl s_client -connect example.com:443 \
-servername example.com
```

Temporary testing only:

```bash
curl -k https://example.com
```

> `-k` disables certificate verification and should not be treated as a permanent fix.

---

# 🔄 HTTP 301 or 302 Redirect

Example:

```text
HTTP/1.1 301 Moved Permanently
```

Follow redirects:

```bash
curl -L http://example.com
```

Inspect headers:

```bash
curl -I http://example.com
```

---

# ❌ HTTP 404

Meaning:

```text
The server responded,
but the requested path was not found.
```

Check:

```bash
curl -v http://server/path
```

This is usually an application or web-server configuration issue, not a basic networking failure.

---

# ❌ HTTP 502 or 503

Likely causes:

- Reverse proxy cannot reach backend
- Backend service is unavailable
- Wrong service port
- Kubernetes Service has no healthy endpoints

Check:

```bash
curl -v <backend-url>
sudo ss -tulpn
systemctl status <backend-service>
```

In Kubernetes:

```bash
kubectl get services
kubectl get endpoints
kubectl get pods
```

---

# 🧱 MTU Problems

Symptoms:

- Small packets work
- Large transfers fail
- VPN or overlay networking behaves inconsistently
- Connections hang after establishment

Test:

```bash
ping -M do -s 1472 <destination>
```

Check interface MTU:

```bash
ip link show
```

Example:

```text
mtu 1500
```

MTU problems are common with:

- VPNs
- VXLAN
- Kubernetes CNIs
- Cloud networks

---

# 🕵️ Packets Arrive but No Reply Leaves

Capture:

```bash
sudo tcpdump -nn -i any \
host <client-ip> and port <port>
```

Possible causes:

- Local firewall
- Application failure
- Incorrect return route
- Asymmetric routing

Check:

```bash
ip route get <client-ip>
sudo ss -tulpn
firewall-cmd --list-all
```

---

# 🕵️ No Packets Arrive

Possible causes:

- Wrong destination IP
- Client-side issue
- Routing failure
- Upstream firewall
- Load balancer problem

Capture:

```bash
sudo tcpdump -nn -i any \
host <client-ip>
```

If nothing appears, investigate before the traffic reaches the server.

---

# ☸️ Kubernetes Common Symptoms

| Symptom | First Checks |
|---|---|
| Node is `NotReady` | `ip addr`, `ip route`, kubelet status |
| Pod cannot resolve names | CoreDNS, `/etc/resolv.conf`, `dig` |
| Service is unreachable | Service, endpoints, target port |
| Ingress returns `503` | Ingress controller, Service, endpoints |
| Pods cannot communicate | CNI, routes, NetworkPolicy |
| API server unreachable | Port `6443`, routing, firewall |
| kubelet unreachable | Port `10250`, service, firewall |

Useful commands:

```bash
kubectl get nodes -o wide
kubectl get pods -o wide
kubectl get services
kubectl get endpoints
kubectl get networkpolicies
```

---

# 📚 Symptom-to-Command Cheat Sheet

| Symptom | First Command |
|---|---|
| Interface unavailable | `ip link` |
| No IP address | `ip addr` |
| No Internet | `ip route` |
| Cannot reach gateway | `ping <gateway>` |
| Hostname fails | `dig <hostname>` |
| Port unavailable | `ss -tulpn` |
| Remote port fails | `nc -zv HOST PORT` |
| Web service fails | `curl -v URL` |
| Firewall suspected | `firewall-cmd --list-all` |
| Traffic unclear | `tcpdump -nn -i any` |
| Route unclear | `ip route get DESTINATION` |
| DNS server suspected | `dig @SERVER HOSTNAME` |

---

# ✅ Final Troubleshooting Order

```text
1. Interface
      ↓
2. IP Address
      ↓
3. Route
      ↓
4. Gateway
      ↓
5. DNS
      ↓
6. Listening Port
      ↓
7. Firewall
      ↓
8. Application
      ↓
9. Packet Capture
```

---

# Conclusion

Most Linux network errors become easier to understand when the symptom is matched to the correct layer.

The essential habit is:

```text
Observe first
      ↓
Identify the failed layer
      ↓
Change one thing
      ↓
Test again
```

A structured troubleshooting process is faster, safer, and more reliable than randomly changing network configuration.