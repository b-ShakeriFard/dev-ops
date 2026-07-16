# 🚦 Network Troubleshooting Flow

| Question | Command |
|---|---|
| Is my interface working? | `ip link` |
| Do I have an IP address? | `ip addr` |
| Do I have a route? | `ip route` |
| Can I reach another host? | `ping` |
| Can DNS resolve names? | `dig` |
| Is a service listening? | `ss -tulpn` |
| Does the application respond? | `curl` |
| What are packets doing? | `tcpdump` |

---

### Thinking like an administrator

Interface
    ↓ 

IP Address
    ↓

Route
    ↓

Connectivity
    ↓

DNS
    ↓

Port
    ↓

Application
    ↓

Packet Analysis