# Computer Networks

## Overview

**Computer Networks** are the backbone of modern computing. Whether you're building a microservices architecture, debugging API latency, or configuring a load balancer, a solid understanding of networking principles is essential. This tutorial covers network models, protocols, addressing, and the practical concepts every backend developer must know to design, deploy, and troubleshoot networked applications.

> **Key Insight**: Networks are about communication. Understanding the rules (protocols), addressing (IPs, ports), and architecture (layers, topologies) enables you to build systems that communicate efficiently, securely, and reliably.

---

## Network Models

### The OSI Model (7 Layers)

The **OSI (Open Systems Interconnection)** model is a conceptual framework that standardizes network communication into seven layers.

```
┌─────────────────────────────────────────────┐
│  7. Application   │  HTTP, HTTPS, FTP, DNS   │  ← What users see
├─────────────────────────────────────────────┤
│  6. Presentation  │  TLS/SSL, Encoding       │  ← Data formatting
├─────────────────────────────────────────────┤
│  5. Session       │  NetBIOS, RPC            │  ← Connection management
├─────────────────────────────────────────────┤
│  4. Transport     │  TCP, UDP                │  ← Reliable delivery
├─────────────────────────────────────────────┤
│  3. Network       │  IP, ICMP, Routing       │  ← Addressing & routing
├─────────────────────────────────────────────┤
│  2. Data Link     │  Ethernet, MAC, Switches │  ← Node-to-node delivery
├─────────────────────────────────────────────┤
│  1. Physical      │  Cables, WiFi, Hubs      │  ← Raw bits transmission
└─────────────────────────────────────────────┘
```

### The TCP/IP Model (4 Layers)

The **TCP/IP model** is the practical implementation used on the internet.

| TCP/IP Layer | Maps to OSI | Protocols | Responsibility |
|-------------|-------------|-----------|----------------|
| **Application** | 5-7 | HTTP, FTP, DNS, SSH | User-facing protocols |
| **Transport** | 4 | TCP, UDP | End-to-end communication |
| **Internet** | 3 | IP, ICMP, ARP | Addressing and routing |
| **Network Access** | 1-2 | Ethernet, WiFi, PPP | Physical transmission |

```
Application Layer     HTTP Request
       ↓                    ↓
Transport Layer       TCP Segment  →  Encapsulated in IP
       ↓                    ↓
Internet Layer        IP Packet    →  Encapsulated in Frame
       ↓                    ↓
Network Access        Ethernet Frame → Sent over wire/air
```

---

## Network Topologies

### Common Topologies

```
Star Topology              Bus Topology            Mesh Topology

     ┌───┐                    ┌───┐                  ┌───┐────┌───┐
     │ A │                    │ A │                  │ A │\  /│ B │
     └─┬─┘                    └──┬──┘                └──┬─┘ \/ └──┬─┘
       │                         │                      │  /\  │
   ┌───┴───┐                 ┌───┴───┐                └─┬─┘/  \└─┬─┘
   │       │                 │       │                  │/    \│
 ┌─┴─┐   ┌─┴─┐             ┌─┴─┐   ┌─┴─┐              └───┐  ┌───┘
 │ B │   │ C │             │ B │   │ C │                  │  │
 └───┘   └───┘             └───┘   └───┘                ┌─┴──┴─┐
                                                         │  D   │
                                                         └──────┘
```

| Topology | Pros | Cons |
|----------|------|------|
| **Star** | Easy to manage, single failure doesn't affect others | Central point of failure |
| **Bus** | Simple, cheap cabling | Difficult to troubleshoot, single cable failure breaks all |
| **Mesh** | Highly redundant, fault-tolerant | Expensive, complex to configure |
| **Tree** | Scalable, hierarchical | Root failure affects subtree |

### Modern Cloud Topologies

```
                    ┌─────────────┐
                    │   Internet  │
                    └──────┬──────┘
                           │
                    ┌──────┴──────┐
                    │ Load Balancer│
                    └──────┬──────┘
                           │
           ┌───────────────┼───────────────┐
           ↓               ↓               ↓
      ┌─────────┐    ┌─────────┐    ┌─────────┐
      │  Web    │    │   Web   │    │   Web   │
      │ Server 1│    │ Server 2│    │ Server 3│
      └────┬────┘    └────┬────┘    └────┬────┘
           │               │               │
           └───────────────┼───────────────┘
                           ↓
                    ┌─────────────┐
                    │   Database  │
                    │   Cluster   │
                    └─────────────┘
```

---

## Network Addressing

### MAC Addresses

- **Media Access Control** address: hardware identifier
- Format: `00:1A:2B:3C:4D:5E` (6 bytes, hex)
- Unique to each network interface card (NIC)
- Used at the Data Link layer for local network communication

```bash
# View MAC addresses
ifconfig | grep ether    # macOS/Linux
ip link show             # Linux
getmac                   # Windows
```

### IP Addresses and Subnetting

An IP address consists of a **network portion** and a **host portion**.

```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
                              
Network:       192.168.1.0     ← First 3 octets (24 bits)
Host:                 .100     ← Last octet (8 bits)
```

**CIDR Notation:**
```
192.168.1.0/24  →  256 addresses (254 usable hosts)
10.0.0.0/8      →  16.7 million addresses
172.16.0.0/12   →  ~1 million addresses
```

| CIDR | Subnet Mask | Usable Hosts |
|------|-------------|--------------|
| /24 | 255.255.255.0 | 254 |
| /16 | 255.255.0.0 | 65,534 |
| /8 | 255.0.0.0 | 16,777,214 |
| /32 | 255.255.255.255 | 1 (single host) |

### Network Address Translation (NAT)

**NAT** allows multiple devices on a private network to share a single public IP address.

```
Private Network                        Internet
┌─────────┐                           ┌─────────┐
│  Laptop │──┐                        │         │
│ 10.0.0.2│  │   ┌─────────┐         │  Server │
└─────────┘  ├──→│  Router │────────→│ 8.8.8.8 │
┌─────────┐  │   │ (NAT)   │         │         │
│  Phone  │──┘   │ 1.2.3.4 │         └─────────┘
│ 10.0.0.3│      └─────────┘
└─────────┘
```

**Types of NAT:**
- **Static NAT** — one-to-one mapping (public ↔ private)
- **Dynamic NAT** — pool of public IPs mapped dynamically
- **PAT/NAPT** — many-to-one using port numbers (most common)

---

## Network Protocols

### HTTP/HTTPS (Application Layer)

Already covered in "How Internet Works." Key reminder:

| Version | Features |
|---------|----------|
| HTTP/1.1 | Persistent connections, chunked transfer |
| HTTP/2 | Multiplexing, server push, header compression |
| HTTP/3 | QUIC (UDP-based), faster connection setup |

### WebSocket

Full-duplex communication over a single TCP connection.

```javascript
// Client
const ws = new WebSocket("wss://chat.example.com");
ws.onopen = () => ws.send("Hello!");
ws.onmessage = (event) => console.log(event.data);

// Server (Node.js with ws library)
const WebSocket = require("ws");
const wss = new WebSocket.Server({ port: 8080 });
wss.on("connection", (ws) => {
  ws.on("message", (message) => {
    ws.send(`Echo: ${message}`);
  });
});
```

### FTP/SFTP (File Transfer)

| Protocol | Port | Security | Use Case |
|----------|------|----------|----------|
| FTP | 21 | None | Legacy file transfers |
| FTPS | 21 + 990 | TLS/SSL | Secure FTP |
| SFTP | 22 | SSH | Secure file transfers (preferred) |
| SCP | 22 | SSH | Secure copy |

```bash
# SFTP
sftp user@remote-host
put localfile.txt /remote/path/
get /remote/file.txt ./local/

# SCP
scp file.txt user@remote:/path/
scp -r user@remote:/path/ ./local/
```

### SSH (Secure Shell)

SSH provides encrypted remote access to servers.

```bash
# Basic connection
ssh user@server.com

# With key
ssh -i ~/.ssh/id_rsa user@server.com

# Port forwarding (local → remote)
ssh -L 8080:localhost:3000 user@server.com

# Port forwarding (remote → local)
ssh -R 9090:localhost:8080 user@server.com
```

### SMTP/IMAP/POP3 (Email)

| Protocol | Port | Purpose |
|----------|------|---------|
| SMTP | 25/587/465 | Sending email |
| IMAP | 143/993 | Reading email (keeps on server) |
| POP3 | 110/995 | Reading email (downloads locally) |

---

## Firewalls and Security

### Types of Firewalls

```
┌─────────────────────────────────────────┐
│           Packet-Filter Firewall        │
│  Rules: Allow TCP port 80, 443          │
│         Deny all others                 │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│        Stateful Inspection Firewall     │
│  Tracks connection state                │
│  Allows return traffic for established  │
│  connections                            │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│         Application Firewall (WAF)      │
│  Inspects HTTP traffic                  │
│  Blocks SQL injection, XSS              │
└─────────────────────────────────────────┘
```

### iptables (Linux Firewall)

```bash
# List rules
sudo iptables -L -v -n

# Allow SSH
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP/HTTPS
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Drop everything else
sudo iptables -P INPUT DROP

# Save rules
sudo iptables-save > /etc/iptables/rules.v4
```

### Common Ports and Services

| Port | Protocol | Service |
|------|----------|---------|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP |
| 53 | UDP/TCP | DNS |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 3306 | TCP | MySQL |
| 5432 | TCP | PostgreSQL |
| 27017 | TCP | MongoDB |
| 6379 | TCP | Redis |
| 8080 | TCP | HTTP Alternative |

---

## Proxies and Load Balancers

### Types of Proxies

```
Forward Proxy                          Reverse Proxy

  Client ──→ Proxy ──→ Server          Internet ──→ Proxy ──→ Server
    (hides client)                           (hides server)

Use: Bypass filters,                      Use: Load balancing,
     Privacy, caching                          SSL termination,
                                               Security
```

### Reverse Proxy with Nginx

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Load Balancing Algorithms

| Algorithm | Description | Use Case |
|-----------|-------------|----------|
| **Round Robin** | Distribute sequentially | Equal capacity servers |
| **Least Connections** | Send to server with fewest connections | Variable request times |
| **IP Hash** | Client IP determines server | Session persistence |
| **Weighted** | Distribute by server capacity | Mixed capacity servers |
| **Health Checks** | Route only to healthy servers | High availability |

```nginx
upstream backend {
    least_conn;
    server 10.0.0.1:3000 weight=3;
    server 10.0.0.2:3000 weight=2;
    server 10.0.0.3:3000 backup;
}
```

---

## Network Troubleshooting Tools

### ping

Test connectivity to a host.

```bash
ping google.com
ping -c 4 google.com        # Send 4 packets (Linux/macOS)
ping -t google.com          # Continuous (Windows)
```

### traceroute / tracert

Trace the route packets take to a destination.

```bash
traceroute google.com       # macOS/Linux
tracert google.com          # Windows
traceroute -I google.com    # Use ICMP instead of UDP
```

### netstat / ss

View network connections and listening ports.

```bash
netstat -tuln               # Show TCP/UDP listening ports
ss -tuln                    # Modern replacement (faster)
lsof -i :3000               # What's using port 3000?
```

### curl

Test HTTP endpoints.

```bash
curl -I https://example.com              # Headers only
curl -v https://example.com              # Verbose
curl -X POST -d '{"key":"val"}' \
     -H "Content-Type: application/json" \
     https://api.example.com/data
```

### nmap

Network scanning and security auditing.

```bash
nmap localhost               # Scan local ports
nmap -p 1-65535 example.com  # Full port scan
nmap -sV example.com         # Detect service versions
```

### tcpdump / Wireshark

Packet capture and analysis.

```bash
# Capture HTTP traffic
sudo tcpdump -i any port 80 -w capture.pcap

# Capture specific host
sudo tcpdump host 192.168.1.1

# Read capture file
wireshark capture.pcap
```

---

## Common Mistakes

### Mistake 1: Binding to 127.0.0.1 in Production

```
❌ app.listen(3000, '127.0.0.1')

   This only accepts connections from localhost.
   External requests will be rejected.

✅ app.listen(3000, '0.0.0.0')  # Accept from any interface
   Or use a reverse proxy (Nginx) in front
```

### Mistake 2: Exposing Database Ports Publicly

```
❌ MongoDB on 0.0.0.0:27017 with no auth

✅ Bind to 127.0.0.1
   Use authentication
   Firewall block port 27017 externally
   Access via VPN or SSH tunnel
```

### Mistake 3: Not Understanding Subnet Masks

```
❌ "192.168.1.0/16 and 192.168.2.0/16 are different networks"

   With /16, both are in 192.168.0.0/16!

✅ /24: 192.168.1.0 - 192.168.1.255
   /16: 192.168.0.0 - 192.168.255.255
   Always calculate the network range
```

### Mistake 4: Confusing Latency and Bandwidth

```
❌ "My 1 Gbps connection is fast for everything"

✅ Bandwidth = max throughput (Mbps/Gbps)
   Latency = round-trip time (ms)

   A 1 Gbps satellite link has high bandwidth
   but terrible latency (500ms+).
   Real-time apps care about latency.
```

### Mistake 5: Ignoring Network Timeouts

```javascript
// ❌ No timeout — hangs forever on network issues
const response = await fetch("https://api.example.com/data");

// ✅ Always set timeouts
const controller = new AbortController();
const timeout = setTimeout(() => controller.abort(), 5000);

const response = await fetch("https://api.example.com/data", {
  signal: controller.signal
});
clearTimeout(timeout);
```

---

## Practice Exercises

### Exercise 1: Map Your Network

```bash
# Find your network details
ifconfig          # or ip addr
route -n          # or ip route

# Scan your local network
nmap -sn 192.168.1.0/24

# What's your public IP?
curl ifconfig.me
curl icanhazip.com
```

Document: your IP, subnet mask, default gateway, DNS servers.

### Exercise 2: Analyze Network Traffic

```bash
# Capture traffic while loading a website
sudo tcpdump -i any port 443 -w website.pcap

# Analyze with tshark
tshark -r website.pcap -Y "tls.handshake.type == 1"
```

How many TCP connections were opened? How many packets exchanged?

### Exercise 3: Test Firewall Rules

Set up a simple firewall and test:

```bash
# Block a port
sudo iptables -A INPUT -p tcp --dport 8080 -j DROP

# Test from another machine
nc -zv your-ip 8080   # Should fail

# Allow again
sudo iptables -D INPUT -p tcp --dport 8080 -j DROP
```

### Exercise 4: Load Balancer Simulation

Create a simple Node.js load balancer:

```javascript
const http = require("http");
const httpProxy = require("http-proxy");

const proxy = httpProxy.createProxyServer();
const servers = ["http://localhost:3001", "http://localhost:3002"];
let current = 0;

http.createServer((req, res) => {
  const target = servers[current++ % servers.length];
  proxy.web(req, res, { target });
}).listen(3000);
```

Test it with multiple backend servers.

### Exercise 5: Network Diagram

Draw a network architecture for:

1. A small web application (single server)
2. A medium application (load balancer + 3 app servers + database)
3. A global SaaS application (multi-region, CDN, microservices)

Include: firewalls, load balancers, databases, caches, and CDNs.

---

## Summary

- The **OSI model** has 7 conceptual layers; **TCP/IP** has 4 practical layers
- **Network topologies** (star, bus, mesh, tree) determine reliability and complexity
- **MAC addresses** identify hardware; **IP addresses** identify network locations
- **Subnetting** divides networks using subnet masks and CIDR notation
- **NAT** allows private networks to share public IP addresses
- Key **protocols**: HTTP/HTTPS (web), SSH (remote access), FTP/SFTP (files), SMTP/IMAP (email), WebSocket (real-time)
- **Firewalls** control traffic using rules at packet, state, or application level
- **Proxies** forward requests (forward = client-side, reverse = server-side)
- **Load balancers** distribute traffic using algorithms like round-robin, least connections, or IP hash
- Essential **troubleshooting tools**: ping, traceroute, netstat, curl, nmap, tcpdump
- Always set **timeouts** on network requests and never expose databases publicly

---

## Next Steps

- **Linux Fundamentals** — master the command line and server administration
- **Git and Version Control** — manage code across distributed teams
- **Backend Development with Node.js** — build networked applications
- **System Design** — design scalable network architectures

Happy coding! 🚀
