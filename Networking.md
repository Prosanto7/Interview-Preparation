# 🌐 Networking Fundamentals

> Essential networking concepts every software engineer should understand - from basics to advanced.

---

## 📚 Table of Contents

1. [What is Networking?](#-what-is-networking)
2. [OSI Model](#-osi-model)
3. [TCP/IP Model](#-tcpip-model)
4. [IP Addresses](#-ip-addresses)
5. [IP Address Classes](#-ip-address-classes)
6. [Subnetting](#-subnetting)
7. [Network Part vs Host Part](#-network-part-vs-host-part)
8. [Subnet Mask](#-subnet-mask)
9. [CIDR Notation](#-cidr-notation)
10. [Public vs Private IP Addresses](#-public-vs-private-ip-addresses)
11. [NAT (Network Address Translation)](#-nat-network-address-translation)
12. [Ports and Protocols](#-ports-and-protocols)
13. [DNS (Domain Name System)](#-dns-domain-name-system)
14. [Common Networking Tools](#-common-networking-tools)
15. [References](#-references)

---

## 🎯 What is Networking?

**Computer Networking** is the practice of connecting computers and devices together to share resources, exchange data, and communicate.

### Key Concepts

| Concept | Description |
|---------|-------------|
| **Network** | Two or more devices connected together |
| **Protocol** | Rules for communication (e.g., TCP/IP, HTTP) |
| **IP Address** | Unique identifier for devices on a network |
| **Router** | Forwards data between networks |
| **Switch** | Connects devices within a network |
| **Gateway** | Entry/exit point for network traffic |

---

## 📊 OSI Model

The **Open Systems Interconnection (OSI)** model is a 7-layer conceptual framework for understanding network communication.

| Layer | Name | Function | Protocols/Examples | Data Unit |
|-------|------|----------|-------------------|-----------|
| **7** | Application | User interface, applications | HTTP, FTP, SMTP, DNS | Data |
| **6** | Presentation | Data formatting, encryption | SSL/TLS, JPEG, ASCII | Data |
| **5** | Session | Manages sessions/connections | NetBIOS, RPC | Data |
| **4** | Transport | End-to-end communication, reliability | TCP, UDP | Segment |
| **3** | Network | Routing, IP addressing | IP, ICMP, ARP | Packet |
| **2** | Data Link | MAC addressing, error detection | Ethernet, Wi-Fi | Frame |
| **1** | Physical | Physical transmission (cables, signals) | Ethernet cables, fiber | Bit |

### Mnemonic
**"All People Seem To Need Data Processing"** (Top to Bottom)

---

## 🔄 TCP/IP Model

A simplified 4-layer model actually used in practice:

| Layer | Name | OSI Equivalent | Protocols |
|-------|------|----------------|-----------|
| **4** | Application | Layers 5-7 | HTTP, FTP, SMTP, DNS |
| **3** | Transport | Layer 4 | TCP, UDP |
| **2** | Internet | Layer 3 | IP, ICMP, ARP |
| **1** | Network Access | Layers 1-2 | Ethernet, Wi-Fi |

---

## 🔢 IP Addresses

An **IP Address** is a unique numerical identifier assigned to each device on a network.

### IPv4 (Internet Protocol version 4)

- **Format:** Four octets separated by dots
- **Size:** 32 bits (4 bytes)
- **Example:** `192.168.1.1`
- **Total addresses:** ~4.3 billion (2³²)
- **Range:** `0.0.0.0` to `255.255.255.255`

**Structure:**
```
192.168.1.100
 │   │  │  │
 │   │  │  └─ Each octet: 0-255 (8 bits)
 └───┴──┴──── Four octets = 32 bits total
```

### IPv6 (Internet Protocol version 6)

- **Format:** Eight groups of hexadecimal digits
- **Size:** 128 bits
- **Example:** `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- **Shortened:** `2001:db8:85a3::8a2e:370:7334`
- **Total addresses:** 340 undecillion (2¹²⁸)

**Why IPv6?**
- IPv4 addresses are exhausted
- Simplified routing
- Better security (IPsec built-in)
- No NAT required

---

## 📋 IP Address Classes

IPv4 addresses are divided into **5 classes** (A, B, C, D, E) based on their first octet.

### Classful Addressing

| Class | First Octet Range | Default Subnet Mask | Network Bits | Host Bits | # of Networks | # of Hosts per Network | Use Case |
|-------|-------------------|---------------------|--------------|-----------|---------------|------------------------|----------|
| **A** | 1 - 126 | 255.0.0.0 (/8) | 8 | 24 | 126 | 16,777,214 | Large organizations |
| **B** | 128 - 191 | 255.255.0.0 (/16) | 16 | 16 | 16,384 | 65,534 | Medium organizations |
| **C** | 192 - 223 | 255.255.255.0 (/24) | 24 | 8 | 2,097,152 | 254 | Small networks |
| **D** | 224 - 239 | N/A | N/A | N/A | N/A | N/A | Multicast |
| **E** | 240 - 255 | N/A | N/A | N/A | N/A | N/A | Experimental |

### Important Notes

**Class A:**
- First bit is always `0`
- Example: `10.0.0.1` (private), `8.8.8.8` (Google DNS - public)
- `127.x.x.x` reserved for loopback (localhost)

**Class B:**
- First two bits are always `10`
- Example: `172.16.0.1` (private), `172.217.14.206` (public)

**Class C:**
- First three bits are always `110`
- Example: `192.168.1.1` (private - most home routers)

**Class D:**
- First four bits are `1110`
- Used for multicast groups (streaming, video conferencing)

**Class E:**
- Reserved for research and future use

### Why Classful Addressing is Obsolete

**Problem:** Wasteful allocation
- A small company getting a Class B address wastes 65,000+ addresses
- Led to IPv4 address exhaustion

**Solution:** **CIDR (Classless Inter-Domain Routing)**
- More flexible subnet masks
- Example: Instead of only /8, /16, /24, you can use /20, /22, /25, etc.

---

## 🔍 Network Part vs Host Part

Every IP address consists of two parts:

```
192.168.1.100
└─────┬─────┘─┬─
  Network  Host
   Part    Part
```

### Network Part
- Identifies the **network** the device belongs to
- All devices on the same network share the same network part
- Determined by the subnet mask

### Host Part
- Identifies the **specific device** within that network
- Must be unique within the network
- Cannot be all 0s (network address) or all 1s (broadcast address)

### Example

**IP Address:** `192.168.1.100`  
**Subnet Mask:** `255.255.255.0` (/24)

| Component | Binary | Decimal |
|-----------|--------|---------|
| IP Address | `11000000.10101000.00000001.01100100` | `192.168.1.100` |
| Subnet Mask | `11111111.11111111.11111111.00000000` | `255.255.255.0` |
| Network Part | `11000000.10101000.00000001` | `192.168.1` |
| Host Part | `01100100` | `100` |

**Result:**
- **Network Address:** `192.168.1.0`
- **Host ID:** `100`
- **Broadcast Address:** `192.168.1.255`

---

## 🎭 Subnet Mask

A **subnet mask** separates the network portion from the host portion of an IP address.

### How It Works

The subnet mask is a 32-bit number with:
- **1s** indicating the network part
- **0s** indicating the host part

### Common Subnet Masks

| CIDR | Subnet Mask | Binary | # of Hosts | Use Case |
|------|-------------|--------|-----------|----------|
| /8 | 255.0.0.0 | `11111111.00000000.00000000.00000000` | 16,777,214 | Class A |
| /16 | 255.255.0.0 | `11111111.11111111.00000000.00000000` | 65,534 | Class B |
| /24 | 255.255.255.0 | `11111111.11111111.11111111.00000000` | 254 | Class C, Home networks |
| /25 | 255.255.255.128 | `11111111.11111111.11111111.10000000` | 126 | Small subnet |
| /26 | 255.255.255.192 | `11111111.11111111.11111111.11000000` | 62 | Smaller subnet |
| /27 | 255.255.255.224 | `11111111.11111111.11111111.11100000` | 30 | Tiny subnet |
| /28 | 255.255.255.240 | `11111111.11111111.11111111.11110000` | 14 | Very small |
| /30 | 255.255.255.252 | `11111111.11111111.11111111.11111100` | 2 | Point-to-point links |
| /32 | 255.255.255.255 | `11111111.11111111.11111111.11111111` | 1 | Single host |

### Calculating Network Address

Use **bitwise AND** operation:

```
IP Address:    192.168.1.100  → 11000000.10101000.00000001.01100100
Subnet Mask:   255.255.255.0  → 11111111.11111111.11111111.00000000
                                 ─────────────────────────────────────
Network Addr:  192.168.1.0    → 11000000.10101000.00000001.00000000
```

---

## 🔢 CIDR Notation

**CIDR (Classless Inter-Domain Routing)** notation uses a slash followed by the number of network bits.

### Format

```
IP_Address/Prefix_Length
192.168.1.0/24
```

### Examples

| CIDR | Subnet Mask | Hosts | Network Range |
|------|-------------|-------|---------------|
| `10.0.0.0/8` | 255.0.0.0 | 16,777,214 | 10.0.0.0 - 10.255.255.255 |
| `172.16.0.0/12` | 255.240.0.0 | 1,048,574 | 172.16.0.0 - 172.31.255.255 |
| `192.168.0.0/16` | 255.255.0.0 | 65,534 | 192.168.0.0 - 192.168.255.255 |
| `192.168.1.0/24` | 255.255.255.0 | 254 | 192.168.1.0 - 192.168.1.255 |
| `192.168.1.128/25` | 255.255.255.128 | 126 | 192.168.1.128 - 192.168.1.255 |

### Quick Calculation Formula

**Number of usable hosts = 2^(32 - prefix) - 2**

Examples:
- `/24`: 2^(32-24) - 2 = 2^8 - 2 = 254 hosts
- `/16`: 2^(32-16) - 2 = 2^16 - 2 = 65,534 hosts
- `/30`: 2^(32-30) - 2 = 2^2 - 2 = 2 hosts

**Why subtract 2?**
- Network address (all host bits 0)
- Broadcast address (all host bits 1)

---

## 🌍 Subnetting

**Subnetting** is dividing a large network into smaller sub-networks (subnets).

### Why Subnet?

| Benefit | Description |
|---------|-------------|
| **Efficient Use of IPs** | Allocate only needed addresses |
| **Better Performance** | Smaller broadcast domains |
| **Security** | Isolate departments/services |
| **Easier Management** | Organize by function/location |

### Subnetting Example

**Original Network:** `192.168.1.0/24` (254 hosts)

**Goal:** Create 4 subnets

**Solution:** Use `/26` (62 hosts per subnet)

| Subnet # | Network Address | Range | Broadcast | Usable IPs |
|----------|-----------------|-------|-----------|------------|
| 1 | 192.168.1.0/26 | .1 - .62 | .63 | 62 |
| 2 | 192.168.1.64/26 | .65 - .126 | .127 | 62 |
| 3 | 192.168.1.128/26 | .129 - .190 | .191 | 62 |
| 4 | 192.168.1.192/26 | .193 - .254 | .255 | 62 |

### Subnetting Steps

1. **Determine requirements:** How many subnets? How many hosts per subnet?
2. **Calculate prefix length:** Find the smallest CIDR that fits
3. **Calculate subnet mask:** Convert prefix to dotted decimal
4. **List subnets:** Calculate network, first, last, and broadcast addresses

### Variable Length Subnet Masking (VLSM)

**VLSM** allows different subnet sizes within the same network.

**Example:**
- Department A needs 100 hosts → `/25` (126 hosts)
- Department B needs 50 hosts → `/26` (62 hosts)
- Department C needs 10 hosts → `/28` (14 hosts)

---

## 🔒 Public vs Private IP Addresses

### Public IP Addresses

- **Globally unique** and routable on the internet
- Assigned by ISPs
- Required to communicate over the internet
- Limited supply (IPv4)

**Examples:**
- `8.8.8.8` (Google DNS)
- `1.1.1.1` (Cloudflare DNS)
- Your home's public IP (check at https://whatismyipaddress.com)

### Private IP Addresses

Defined by **RFC 1918**, these addresses are **not routable** on the public internet.

| Class | Range | CIDR | # of Addresses |
|-------|-------|------|----------------|
| **A** | 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 | 16,777,216 |
| **B** | 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 | 1,048,576 |
| **C** | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 | 65,536 |

**Characteristics:**
- Used in internal/local networks
- Can be reused in different networks
- Require NAT to access the internet
- More secure (not directly accessible from internet)

**Use Cases:**
- Home networks: `192.168.1.x`
- Corporate networks: `10.x.x.x` or `172.16.x.x`
- AWS VPCs: Often `10.0.0.0/16` or `172.31.0.0/16`

### Special IP Addresses

| Address | Purpose |
|---------|---------|
| `0.0.0.0` | Default route, all addresses |
| `127.0.0.1` | Localhost/loopback |
| `127.0.0.0/8` | Loopback range |
| `169.254.0.0/16` | APIPA (Automatic Private IP Addressing) |
| `224.0.0.0 - 239.255.255.255` | Multicast |
| `255.255.255.255` | Broadcast |

---

## 🔄 NAT (Network Address Translation)

**NAT** translates private IP addresses to public IP addresses, allowing multiple devices to share a single public IP.

### How NAT Works

```
Private Network                    Router (NAT)              Internet
┌─────────────┐                   ┌──────────┐             ┌─────────┐
│ 192.168.1.10│───────────────────│          │             │         │
│ 192.168.1.11│   Multiple        │  Public  │────────────▶│ Website │
│ 192.168.1.12│   Private IPs     │203.0.113.│ One Public  │         │
└─────────────┘                   │    5     │     IP      └─────────┘
                                  └──────────┘
```

### Types of NAT

| Type | Description | Use Case |
|------|-------------|----------|
| **Static NAT** | 1:1 mapping (one private ↔ one public) | Servers that need consistent external IP |
| **Dynamic NAT** | Pool of public IPs assigned dynamically | Multiple servers sharing public IPs |
| **PAT (Port Address Translation)** | Many private IPs → one public IP (different ports) | Home routers, most common |

### PAT Example (Most Common)

```
Internal Request:
192.168.1.10:5000 → google.com:443

NAT Translation:
203.0.113.5:12345 → google.com:443
(Router remembers: port 12345 maps to 192.168.1.10:5000)

Response:
google.com:443 → 203.0.113.5:12345

NAT Reverse Translation:
Router forwards to 192.168.1.10:5000
```

---

## 🚪 Ports and Protocols

### What is a Port?

A **port** is a logical endpoint for network communication, numbered 0-65535.

### Port Ranges

| Range | Name | Usage |
|-------|------|-------|
| **0-1023** | Well-Known Ports | System/common services |
| **1024-49151** | Registered Ports | Application services |
| **49152-65535** | Dynamic/Private | Temporary client ports |

### Common Ports

| Port | Protocol | Service |
|------|----------|---------|
| 20, 21 | FTP | File Transfer |
| 22 | SSH | Secure Shell |
| 23 | Telnet | Remote login (insecure) |
| 25 | SMTP | Email sending |
| 53 | DNS | Domain name resolution |
| 80 | HTTP | Web traffic |
| 110 | POP3 | Email retrieval |
| 143 | IMAP | Email retrieval |
| 443 | HTTPS | Secure web traffic |
| 3306 | MySQL | Database |
| 5432 | PostgreSQL | Database |
| 6379 | Redis | Cache/Database |
| 27017 | MongoDB | Database |
| 3389 | RDP | Remote Desktop (Windows) |
| 5000 | Flask | Python web framework (dev) |
| 8080 | HTTP Alt | Alternative HTTP port |
| 8443 | HTTPS Alt | Alternative HTTPS port |

### TCP vs UDP

| Feature | TCP | UDP |
|---------|-----|-----|
| **Connection** | Connection-oriented (handshake) | Connectionless |
| **Reliability** | Guaranteed delivery, retransmission | Best effort, no guarantee |
| **Order** | Maintains packet order | No order guarantee |
| **Speed** | Slower (overhead) | Faster (minimal overhead) |
| **Use Case** | HTTP, HTTPS, FTP, Email | DNS, Streaming, VoIP, Gaming |
| **Header Size** | 20 bytes | 8 bytes |

**TCP Three-Way Handshake:**
```
Client          Server
  │──── SYN ────▶│
  │◀── SYN-ACK ──│
  │──── ACK ────▶│
  │              │
```

---

## 🔍 DNS (Domain Name System)

DNS translates human-readable domain names to IP addresses.

### DNS Hierarchy

```
                    . (Root)
                      │
        ┌─────────────┼─────────────┐
       .com          .org          .net     (TLD - Top Level Domain)
        │
    example.com                            (Second Level Domain)
        │
    www.example.com                        (Subdomain)
```

### DNS Record Types

| Record | Purpose | Example |
|--------|---------|---------|
| **A** | Maps domain to IPv4 | `example.com → 93.184.216.34` |
| **AAAA** | Maps domain to IPv6 | `example.com → 2606:2800:220:1:...` |
| **CNAME** | Alias to another domain | `www.example.com → example.com` |
| **MX** | Mail server | `example.com → mail.example.com` |
| **TXT** | Text information | SPF, DKIM, verification |
| **NS** | Nameserver | `example.com → ns1.provider.com` |
| **SOA** | Start of Authority | Zone metadata |
| **PTR** | Reverse DNS (IP → domain) | `34.216.184.93 → example.com` |

### DNS Lookup Process

```
1. User enters: www.example.com
   ↓
2. Browser checks local cache
   ↓
3. OS checks hosts file (/etc/hosts)
   ↓
4. Query local DNS resolver (ISP or 8.8.8.8)
   ↓
5. Recursive query:
   - Root DNS server → .com TLD server → example.com authoritative server
   ↓
6. IP address returned: 93.184.216.34
   ↓
7. Browser connects to IP address
```

### DNS Tools

```bash
# Query DNS records
nslookup example.com
dig example.com
host example.com

# Specific record types
dig example.com A
dig example.com MX
dig example.com TXT

# Trace DNS resolution path
dig +trace example.com
```

---

## 🛠️ Common Networking Tools

### Diagnostic Tools

```bash
# Check connectivity
ping google.com
ping -c 4 8.8.8.8            # Send 4 packets

# Trace route to destination
traceroute google.com         # Linux/Mac
tracert google.com            # Windows

# Show network configuration
ifconfig                      # Linux/Mac (older)
ip addr show                  # Linux (modern)
ipconfig                      # Windows

# Show routing table
route -n                      # Linux
netstat -r                    # Mac/Linux
route print                   # Windows

# DNS lookup
nslookup google.com
dig google.com
host google.com

# Check open ports
netstat -tuln                 # Linux
netstat -an                   # Windows
ss -tuln                      # Linux (modern)

# Port scanning (be careful!)
nmap -p 80,443 example.com

# Show active connections
netstat -ant

# Network performance
iperf3 -c server.com          # Bandwidth testing
mtr google.com                # Combines ping + traceroute

# Packet capture
tcpdump -i eth0              # Capture packets
wireshark                     # GUI packet analyzer

# HTTP requests
curl https://example.com
wget https://example.com
```

### Quick Diagnostics

```bash
# Test if port is open
telnet example.com 80
nc -zv example.com 80

# Check your public IP
curl ifconfig.me
curl ipinfo.io

# Test DNS resolution time
time nslookup google.com

# Check latency
ping -c 10 google.com | tail -1
```

---

## 📚 References

### Official Documentation
- [RFC 791 - Internet Protocol (IPv4)](https://datatracker.ietf.org/doc/html/rfc791)
- [RFC 1918 - Private Address Space](https://datatracker.ietf.org/doc/html/rfc1918)
- [RFC 4632 - CIDR Notation](https://datatracker.ietf.org/doc/html/rfc4632)
- [RFC 793 - TCP](https://datatracker.ietf.org/doc/html/rfc793)
- [RFC 768 - UDP](https://datatracker.ietf.org/doc/html/rfc768)
- [RFC 1035 - DNS](https://datatracker.ietf.org/doc/html/rfc1035)

### Learning Resources
- [Subnet Calculator](https://www.subnet-calculator.com/)
- [CIDR to IPv4 Converter](https://www.ipaddressguide.com/cidr)
- [Visual Subnet Calculator](https://www.davidc.net/sites/default/subnets/subnets.html)
- [Cloudflare Learning Center - Networking](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-network/)
- [Cisco Networking Basics](https://www.cisco.com/c/en/us/solutions/small-business/resource-center/networking/networking-basics.html)

### Interactive Learning
- [Subnet Practice](https://subnettingpractice.com/)
- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) - Network simulator

### Books
- "Computer Networking: A Top-Down Approach" by Kurose & Ross
- "TCP/IP Illustrated" by W. Richard Stevens
- "Network Warrior" by Gary A. Donahue

---

## 🎓 Practice Questions

### Basic
1. What is the difference between TCP and UDP?
2. Convert `192.168.1.0/24` to its subnet mask.
3. How many usable hosts in a `/26` network?
4. What are the private IP ranges?
5. What is the purpose of NAT?

### Intermediate
6. Given `172.16.50.100/20`, what is the network address?
7. Subnet `10.0.0.0/16` into 4 equal subnets.
8. Explain the DNS resolution process.
9. What is the difference between a switch and a router?
10. How does a TCP three-way handshake work?

### Advanced
11. Design a subnet scheme for a company with 5 departments (200, 100, 50, 30, 10 hosts).
12. Calculate VLSM for: Network A (500 hosts), Network B (200 hosts), Network C (50 hosts) from `172.20.0.0/16`.
13. Explain how NAT helps with IPv4 exhaustion.
14. What are the security implications of using public vs private IPs?
15. Design a network topology for a three-tier web application.

---

**Happy Learning! 🚀**
