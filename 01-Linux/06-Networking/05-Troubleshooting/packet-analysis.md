# 🕵️ Network Packet Analysis

> Observing real network traffic with `tcpdump`.

---

## 🎯 What Problem Does It Solve?

Packet analysis answers:

> “Are packets actually reaching the system, and is the system responding?”

Use it when:

- Configuration looks correct
- The service is running
- The port is open
- Connections still fail
- Communication is slow or intermittent

---

## 🧩 Packet Troubleshooting Model

```text
Client Request
      ↓
Network
      ↓
Linux Interface
      ↓
Firewall
      ↓
Application
      ↓
Server Response
```

`tcpdump` shows what is happening at the network interface.

---

## 🔌 List Capture Interfaces

```bash
tcpdump -D
```

Example:

```text
1. ens33
2. lo
3. any
4. cni0
```

Capture from all interfaces:

```bash
sudo tcpdump -i any
```

Capture from one interface:

```bash
sudo tcpdump -i ens33
```

---

## 🔢 Limit the Capture

Capture only 20 packets:

```bash
sudo tcpdump -i ens33 -c 20
```

This prevents the terminal from filling with continuous traffic.

---

## 🌐 Filter by Host

Capture traffic involving one host:

```bash
sudo tcpdump -i any host 192.168.1.50
```

Only traffic sent to or received from that host is displayed.

### Source only

```bash
sudo tcpdump -i any src host 192.168.1.50
```

### Destination only

```bash
sudo tcpdump -i any dst host 192.168.1.50
```

---

## 🚪 Filter by Port

Capture HTTPS traffic:

```bash
sudo tcpdump -i any port 443
```

Capture DNS traffic:

```bash
sudo tcpdump -i any port 53
```

Capture an application port:

```bash
sudo tcpdump -i any port 8080
```

---

## 🚚 Filter by Protocol

### TCP

```bash
sudo tcpdump -i any tcp
```

### UDP

```bash
sudo tcpdump -i any udp
```

### ICMP

```bash
sudo tcpdump -i any icmp
```

ICMP capture is useful while running:

```bash
ping 192.168.1.50
```

---

## 🔗 Combine Filters

Capture traffic to one server and port:

```bash
sudo tcpdump -i any \
host 192.168.1.50 and port 443
```

Capture TCP traffic from a client:

```bash
sudo tcpdump -i any \
tcp and src host 192.168.1.20
```

Capture DNS traffic excluding one host:

```bash
sudo tcpdump -i any \
port 53 and not host 192.168.1.10
```

---

## 🔢 Disable Name Resolution

Use numeric addresses and ports:

```bash
sudo tcpdump -nn -i any
```

Why use `-nn`?

```text
Faster output
No DNS lookups
No service-name conversion
```

Instead of:

```text
server.https
```

you see:

```text
192.168.1.50.443
```

---

## 🚦 Observe the TCP Handshake

A successful TCP connection begins with:

```text
Client → Server : SYN
Server → Client : SYN-ACK
Client → Server : ACK
```

Capture:

```bash
sudo tcpdump -nn -i any \
host 192.168.1.50 and port 443
```

Then connect from the client:

```bash
curl https://192.168.1.50
```

---

## ❌ SYN with No Reply

Capture shows repeated:

```text
Client → Server : SYN
Client → Server : SYN
Client → Server : SYN
```

But no:

```text
SYN-ACK
```

Possible causes:

- Firewall dropping traffic
- Wrong route
- Server unreachable
- Return path problem

---

## 🚫 TCP Reset

Capture shows:

```text
Client → Server : SYN
Server → Client : RST
```

Likely meaning:

```text
Host is reachable
but nothing accepts the connection
```

Check:

```bash
sudo ss -tulpn
```

---

## 🌍 Analyze DNS Traffic

Capture DNS:

```bash
sudo tcpdump -nn -i any port 53
```

In another terminal:

```bash
dig example.com
```

Expected flow:

```text
Client → DNS Server : Query
DNS Server → Client : Response
```

### Query with No Response

Possible causes:

- DNS server unavailable
- Firewall blocks port 53
- Routing problem
- Wrong DNS server configured

---

## 📡 Analyze Ping Traffic

Capture ICMP:

```bash
sudo tcpdump -nn -i any icmp
```

Run:

```bash
ping -c 4 192.168.1.50
```

Expected:

```text
Echo request
Echo reply
```

Only requests and no replies may indicate:

- ICMP blocked
- Host unavailable
- Return route problem

---

## 💾 Save a Capture

Save packets to a file:

```bash
sudo tcpdump -i ens33 \
-w network-capture.pcap
```

Stop with:

```text
Ctrl+C
```

Read it later:

```bash
tcpdump -r network-capture.pcap
```

Apply a display filter while reading:

```bash
tcpdump -nn \
-r network-capture.pcap \
port 443
```

The `.pcap` file can also be opened in Wireshark.

> Packet captures may contain credentials, tokens, internal addresses, and application data. Store and share them carefully.

---

## 🖥️ Real Linux Scenario

Problem:

```text
A remote client cannot reach an application on TCP 8080.
```

### Check the service

```bash
sudo ss -tulpn | grep 8080
```

Expected:

```text
LISTEN 0.0.0.0:8080
```

### Check the firewall

```bash
firewall-cmd \
--query-port=8080/tcp
```

### Start the capture

```bash
sudo tcpdump -nn -i any \
port 8080
```

### Test from the client

```bash
curl http://server-ip:8080
```

### Interpret the result

| Capture Result | Likely Problem |
|---|---|
| No packets arrive | Client, routing, or upstream firewall |
| SYN arrives, no response | Local firewall or return-route issue |
| Server sends RST | Nothing listening or wrong address binding |
| Handshake completes | Network works; inspect the application |
| Request arrives, response leaves | Investigate the client or return path |

---

## ☸️ Kubernetes Connection

On Kubernetes nodes, packet analysis can help inspect:

- Node-to-node communication
- Pod traffic
- Service traffic
- CNI interfaces
- DNS requests
- NetworkPolicy behavior

List interfaces:

```bash
ip link
```

Possible interfaces:

```text
cni0
flannel.1
cilium_host
vxlan.calico
```

Capture Pod traffic:

```bash
sudo tcpdump -nn -i any \
host <pod-ip>
```

Capture Kubernetes API traffic:

```bash
sudo tcpdump -nn -i any \
port 6443
```

Capture CoreDNS traffic:

```bash
sudo tcpdump -nn -i any \
port 53
```

---

## 🛠️ Troubleshooting Workflow

```text
1. Reproduce the problem
        ↓
2. Select the correct interface
        ↓
3. Apply a narrow filter
        ↓
4. Capture request and response
        ↓
5. Identify where communication stops
```

Avoid starting with an unrestricted production capture when a narrow filter can answer the question.

---

## 📚 Quick Reference

| Command | Purpose |
|---|---|
| `tcpdump -D` | List interfaces |
| `tcpdump -i any` | Capture all interfaces |
| `tcpdump -nn` | Disable name and port resolution |
| `tcpdump -c 20` | Stop after 20 packets |
| `tcpdump host IP` | Filter by host |
| `tcpdump src host IP` | Filter by source |
| `tcpdump dst host IP` | Filter by destination |
| `tcpdump port 443` | Filter by port |
| `tcpdump tcp` | Capture TCP |
| `tcpdump udp` | Capture UDP |
| `tcpdump icmp` | Capture ICMP |
| `tcpdump -w file.pcap` | Save packets |
| `tcpdump -r file.pcap` | Read saved packets |

---

## Conclusion

Packet analysis replaces assumptions with evidence.

The key questions are:

```text
Did the request arrive?
        ↓
Did the server reply?
        ↓
Did the reply return to the client?
```

When higher-level tools cannot explain a network failure, `tcpdump` reveals where communication actually stops.