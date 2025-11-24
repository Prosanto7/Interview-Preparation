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

## 📋 IP Address Classes (Classful Addressing - Detailed)

IPv4 addresses were originally divided into **5 classes** (A, B, C, D, E) based on their first octet. This system, called **Classful Addressing**, was used from 1981 to 1993.

### Historical Context

**Why Classful Addressing Was Created:**
- Early internet had few networks, so simple classification seemed sufficient
- Easy to implement in early routers (hardware limitations)
- Predictable address allocation
- Simple routing table lookups

**Timeline:**
- **1981:** RFC 791 introduced classful addressing
- **1981-1993:** Classful routing used exclusively
- **1993:** CIDR introduced (RFC 1517-1520)
- **Today:** Classful addressing obsolete but still taught for understanding

### Classful Address Structure

| Class | First Octet Range | First Bits | Default Subnet Mask | Network Bits | Host Bits | # of Networks | # of Hosts per Network | Use Case |
|-------|-------------------|------------|---------------------|--------------|-----------|---------------|------------------------|----------|
| **A** | 1 - 126 | `0` | 255.0.0.0 (/8) | 8 | 24 | 126 | 16,777,214 | Very large organizations, ISPs |
| **B** | 128 - 191 | `10` | 255.255.0.0 (/16) | 16 | 16 | 16,384 | 65,534 | Medium-large organizations |
| **C** | 192 - 223 | `110` | 255.255.255.0 (/24) | 24 | 8 | 2,097,152 | 254 | Small organizations, LANs |
| **D** | 224 - 239 | `1110` | N/A | N/A | N/A | N/A | N/A | Multicast groups |
| **E** | 240 - 255 | `1111` | N/A | N/A | N/A | N/A | N/A | Reserved for experimental use |

### Class A (1.0.0.0 - 126.255.255.255)

**Binary Pattern:** First bit is always `0`

```
Class A IP: 10.0.0.1
Binary:     00001010.00000000.00000000.00000001
            ↑
            0 = Class A
```

**Characteristics:**
- **First Octet:** 1-126 (0 reserved, 127 for loopback)
- **Default Mask:** `255.0.0.0` (/8)
- **Format:** `N.H.H.H` (N=Network, H=Host)
- **Networks Available:** 126 (2^7 - 2)
  - Why -2? Network 0 and 127 reserved
- **Hosts per Network:** 16,777,214 (2^24 - 2)

**Network Calculation:**
```
IP: 45.123.67.89
Mask: 255.0.0.0

Network Address: 45.0.0.0
Broadcast: 45.255.255.255
First Host: 45.0.0.1
Last Host: 45.255.255.254
```

**Real-World Examples:**
- `10.0.0.0/8` - Private (RFC 1918)
- `8.0.0.0/8` - Google (8.8.8.8, 8.8.4.4)
- `15.0.0.0/8` - HP
- `20.0.0.0/8` - Microsoft Azure

**Problem with Class A:**
```
Company needs 5,000 hosts
Gets Class A: 16,777,214 addresses
Wasted: 16,772,214 addresses (99.97% waste!)
```

### Class B (128.0.0.0 - 191.255.255.255)

**Binary Pattern:** First two bits are always `10`

```
Class B IP: 172.16.0.1
Binary:     10101100.00010000.00000000.00000001
            ↑↑
            10 = Class B
```

**Characteristics:**
- **First Octet:** 128-191
- **Default Mask:** `255.255.0.0` (/16)
- **Format:** `N.N.H.H`
- **Networks Available:** 16,384 (2^14)
- **Hosts per Network:** 65,534 (2^16 - 2)

**Network Calculation:**
```
IP: 172.20.50.100
Mask: 255.255.0.0

Network Address: 172.20.0.0
Broadcast: 172.20.255.255
First Host: 172.20.0.1
Last Host: 172.20.255.254
```

**Real-World Examples:**
- `172.16.0.0/12` - Private (RFC 1918)
- `128.0.0.0/16` - Reserved
- `172.217.0.0/16` - Google services

**Problem with Class B:**
```
Company needs 500 hosts
Gets Class B: 65,534 addresses
Wasted: 65,034 addresses (99.2% waste!)
```

### Class C (192.0.0.0 - 223.255.255.255)

**Binary Pattern:** First three bits are always `110`

```
Class C IP: 192.168.1.1
Binary:     11000000.10101000.00000001.00000001
            ↑↑↑
            110 = Class C
```

**Characteristics:**
- **First Octet:** 192-223
- **Default Mask:** `255.255.255.0` (/24)
- **Format:** `N.N.N.H`
- **Networks Available:** 2,097,152 (2^21)
- **Hosts per Network:** 254 (2^8 - 2)

**Network Calculation:**
```
IP: 200.50.100.25
Mask: 255.255.255.0

Network Address: 200.50.100.0
Broadcast: 200.50.100.255
First Host: 200.50.100.1
Last Host: 200.50.100.254
```

**Real-World Examples:**
- `192.168.0.0/16` - Private (RFC 1918)
- `192.0.2.0/24` - Documentation (TEST-NET-1)
- `198.18.0.0/15` - Benchmark testing

**Problem with Class C:**
```
Company needs 500 hosts
Class C only provides: 254 addresses
Solution: Must use 2 Class C networks
Problem: Inefficient, complex routing
```

### Class D (224.0.0.0 - 239.255.255.255)

**Binary Pattern:** First four bits are `1110`

```
Class D IP: 224.0.0.1
Binary:     11100000.00000000.00000000.00000001
            ↑↑↑↑
            1110 = Class D (Multicast)
```

**Characteristics:**
- **Not for regular hosts** - Multicast group addresses
- **No network/host division**
- **No subnet mask**

**Use Cases:**
- Video streaming
- IPTV
- Routing protocols (OSPF, RIP)
- Network discovery protocols

**Well-Known Multicast Addresses:**
- `224.0.0.1` - All hosts on subnet
- `224.0.0.2` - All routers on subnet
- `224.0.0.5` - All OSPF routers
- `224.0.0.6` - All OSPF DRs
- `239.0.0.0/8` - Organization-local scope

### Class E (240.0.0.0 - 255.255.255.255)

**Binary Pattern:** First four bits are `1111`

```
Class E IP: 250.0.0.1
Binary:     11111010.00000000.00000000.00000001
            ↑↑↑↑
            1111 = Class E (Reserved)
```

**Characteristics:**
- **Reserved for future/experimental use**
- **Never assigned to regular networks**
- **Not routable on the internet**

**Special Addresses:**
- `255.255.255.255` - Limited broadcast address

### How to Identify IP Address Class

**Method 1: First Octet**
```
First Octet    Class
─────────────────────
1   - 126      A
128 - 191      B
192 - 223      C
224 - 239      D
240 - 255      E
```

**Method 2: Binary First Bits**
```
Binary Pattern    Class
──────────────────────
0xxxxxxx          A
10xxxxxx          B
110xxxxx          C
1110xxxx          D
1111xxxx          E
```

**Practice Examples:**
```
IP: 50.100.200.10
First Octet: 50 → Range 1-126 → Class A ✓

IP: 150.25.75.125
First Octet: 150 → Range 128-191 → Class B ✓

IP: 200.10.20.30
First Octet: 200 → Range 192-223 → Class C ✓

IP: 230.0.0.1
First Octet: 230 → Range 224-239 → Class D ✓
```

---

## 🚫 Major Drawbacks of Classful Addressing

### 1. Wasteful IP Address Allocation

**The Core Problem:** Fixed-size address blocks don't match real-world needs.

#### Example 1: Company with 5,000 Employees

```
Requirement: 5,000 IP addresses

Option 1: Class C
─────────────────────────────────────
254 hosts per network
Need: 5000 ÷ 254 = 20 Class C networks
Problems:
  ❌ Managing 20 different networks
  ❌ Complex routing (20 entries)
  ❌ 20 × 254 = 5,080 addresses
  ❌ Waste: 80 addresses
  ❌ Operational nightmare

Option 2: Class B
─────────────────────────────────────
65,534 hosts per network
Allocation: 1 Class B network
Problems:
  ❌ Waste: 60,534 addresses (92% wasted!)
  ❌ Address space exhaustion
  ❌ Unethical use of scarce resources
```

**Real Impact:** In the 1990s, organizations routinely received Class B addresses when they needed only hundreds of IPs, accelerating IPv4 exhaustion.

#### Example 2: Small Business with 100 Devices

```
Requirement: 100 IP addresses

Class C: 254 addresses
─────────────────────────────────────
Allocated: 254 addresses
Actually needed: 100 addresses
Wasted: 154 addresses (60.6% waste)

But at least it fits! (Unlike needing Class B)
```

#### Example 3: Point-to-Point Links

```
Requirement: 2 IP addresses (router-to-router link)

Classful Approach:
─────────────────────────────────────
Minimum allocation: Class C (254 addresses)
Used: 2 addresses
Wasted: 252 addresses (99.2% waste!)

With thousands of links:
  1,000 links × 252 wasted = 252,000 IPs wasted!
```

### 2. Routing Table Explosion

**Problem:** Every classful network needed a separate routing entry.

#### Before CIDR (Classful Routing):

```
Example Organization: Has 16 Class C networks

Routing Table Entries Required:
─────────────────────────────────────
200.50.1.0/24   via 10.0.0.1
200.50.2.0/24   via 10.0.0.1
200.50.3.0/24   via 10.0.0.1
200.50.4.0/24   via 10.0.0.1
... (12 more entries)
200.50.16.0/24  via 10.0.0.1

Total: 16 routing table entries
```

**Impact:**
- Larger routing tables → More memory required
- Slower routing lookups
- Internet routing tables grew from 5,000 entries (1990) to 50,000 (1993)
- Early routers couldn't handle the growth

#### After CIDR (Classless Routing):

```
Same Organization with Route Aggregation:

Routing Table Entry:
─────────────────────────────────────
200.50.0.0/20   via 10.0.0.1

Total: 1 routing table entry (16 networks aggregated!)
```

### 3. Inflexible Network Design

**Problem:** Can't create subnets of appropriate sizes.

#### Example: University Campus Network

```
University Requirements:
─────────────────────────────────────
- Admin Building: 50 hosts
- Library: 100 hosts
- Student Dorm A: 500 hosts
- Student Dorm B: 500 hosts
- Computer Lab: 30 hosts
- WiFi Network: 200 hosts

Classful Approach (Class B: 172.20.0.0):
─────────────────────────────────────
Problem: Can't subdivide flexibly!

Class B gives 65,534 hosts in ONE network
  ❌ All devices in same broadcast domain
  ❌ Security nightmare (no isolation)
  ❌ Performance issues (broadcast storms)
  ❌ Can't apply different policies per department

Could use multiple Class Cs, but:
  ❌ Some departments too big (500 > 254)
  ❌ Some too small (30 << 254, waste 224)
```

### 4. No Support for Subnetting (Initially)

**Problem:** Original classful addressing didn't allow subdivision.

```
Company Has: Class B (172.16.0.0)
Offices: New York, London, Tokyo

Classful Limitation:
─────────────────────────────────────
ALL offices share 172.16.0.0/16
  ❌ Can't isolate office networks
  ❌ All in same broadcast domain
  ❌ Security risk (no segmentation)
  ❌ Performance issues
  ❌ Troubleshooting nightmare
```

**Note:** Subnetting was later added (RFC 950, 1985), but still constrained by class boundaries.

### 5. Poor Utilization Statistics

**Global Impact of Classful Addressing:**

```
Analysis of Allocated Class A & B Networks (Early 1990s):
─────────────────────────────────────────────────────────

Class A Example: MIT (18.0.0.0/8)
  Allocated: 16,777,214 addresses
  Actually used: ~100,000 addresses
  Waste: 16,677,214 addresses (99.4%)

Class B Example: Average Organization
  Allocated: 65,534 addresses
  Average actual use: ~5,000 addresses
  Waste: 60,534 addresses (92.4%)

Total IPv4 Space: 4,294,967,296 addresses
Estimated waste: >50% due to classful allocation
```

### 6. Early IPv4 Exhaustion

**Timeline:**

```
1981: IPv4 introduced (4.3 billion addresses)
      Seemed infinite at the time

1990: Warning signs emerge
      ├─ Class B addresses nearly exhausted
      └─ Internet growth exploding

1993: Crisis point
      ├─ Projected IPv4 exhaustion by 1995!
      ├─ CIDR emergency deployment (RFC 1517-1520)
      └─ Classful routing officially obsolete

2011: IANA exhausts IPv4 pool
2014-2015: Regional registries exhaust their pools

If classful addressing continued:
  IPv4 would have exhausted by 1995-1996!
```

### 7. Routing Protocol Limitations

**Classful Routing Protocols (RIPv1, IGRP):**

```
Limitations:
─────────────────────────────────────
❌ Don't send subnet mask in routing updates
❌ Assume classful boundaries
❌ Can't support VLSM (Variable Length Subnet Masks)
❌ Can't support discontiguous subnets
❌ Automatic summarization at class boundaries

Example Problem:
─────────────────────────────────────
Network 1: 172.16.1.0/24
Network 2: 172.16.2.0/24
Separated by: 10.0.0.0/8

RIPv1 advertises:
  Router A: 172.16.0.0 (summarized to Class B)
  Router B: 172.16.0.0 (summarized to Class B)
  
Result: Routing loop or blackhole! 💥
```

**Classless Routing Protocols (RIPv2, OSPF, BGP):**

```
Improvements:
─────────────────────────────────────
✅ Include subnet mask in updates
✅ Support VLSM
✅ Support CIDR
✅ Support discontiguous subnets
✅ Manual control over summarization
```

### 8. Economic Impact

**Cost of Classful Addressing:**

```
Early Internet Service Providers:
─────────────────────────────────────
Scenario: ISP needs addresses for 10,000 customers

Classful Approach:
  Need Class B: 65,534 addresses
  Cost in early 1990s: $10,000-$50,000 per Class B
  Waste: 55,534 addresses

Classless (CIDR) Approach:
  Need /18: 16,384 addresses
  Cost: Much lower (charged for actual usage)
  Waste: 6,384 addresses (60% reduction in waste)

Savings: Tens of thousands of dollars + better utilization
```

### 9. Real-World Disaster Examples

#### Example 1: Stanford University

```
Allocation: Class A (36.0.0.0/8)
Total IPs: 16,777,214

Actual Need (generous estimate): 100,000
Waste: 16,677,214 addresses

Impact:
  - Could have housed 167 medium organizations
  - Accelerated IPv4 exhaustion
  - Public pressure to return unused space
```

#### Example 2: Ford Motor Company

```
Allocation: Class A (19.0.0.0/8)

Even for a Fortune 500 company:
  Employees: ~200,000 worldwide
  Devices per employee: ~2-3
  Total need: ~600,000 IPs max

Allocated: 16,777,214
Waste: 16,177,214 (96% unused!)
```

#### Example 3: Small ISP Growth Problem

```
ISP Customer Growth:
─────────────────────────────────────
Year 1: 100 customers → Need Class C (254 IPs) ✓
Year 2: 300 customers → Need 2nd Class C
Year 3: 600 customers → Need 3rd Class C
Year 4: 1,000 customers → Need 4th Class C

Problems:
  ❌ Acquiring multiple Class Cs is bureaucratic
  ❌ Non-contiguous address space
  ❌ Routing table bloat (4 entries instead of 1)
  ❌ Can't grow smoothly to 5,000 customers
      (Need Class B, but huge jump/waste)
```

### 10. Comparison Table: Classful vs Real Needs

| Organization Type | Actual Need | Classful Allocation | Waste | Waste % |
|-------------------|-------------|---------------------|-------|---------|
| Home network | 10 IPs | Class C: 254 | 244 | 96% |
| Small business | 50 IPs | Class C: 254 | 204 | 80% |
| Medium business | 500 IPs | Class B: 65,534 | 65,034 | 99.2% |
| Large enterprise | 5,000 IPs | Class B: 65,534 | 60,534 | 92% |
| ISP | 10,000 IPs | Class B: 65,534 | 55,534 | 85% |
| Point-to-point link | 2 IPs | Class C: 254 | 252 | 99.2% |
| Router loopback | 1 IP | Class C: 254 | 253 | 99.6% |

**Average Waste: 85-95% of allocated addresses!**

---

## 🎯 Why CIDR is Better (Detailed Explanation)

**CIDR (Classless Inter-Domain Routing)** was introduced in 1993 to solve the classful addressing crisis.

### What is CIDR?

**CIDR** allows **variable-length subnet masking**, meaning you can use any prefix length from /1 to /32, not just /8, /16, or /24.

**Format:**
```
IP_Address/Prefix_Length
192.168.10.0/24
10.0.0.0/22
172.16.50.0/27
```

### Key CIDR Innovations

#### 1. Flexible Address Allocation

**Classful (Rigid):**
```
Only 3 choices:
  Class A: /8  (16,777,214 hosts)
  Class B: /16 (65,534 hosts)
  Class C: /24 (254 hosts)

If you need 500 hosts:
  ❌ Class C too small (254)
  ❌ Class B too large (65,534)
  ❌ No middle ground!
```

**CIDR (Flexible):**
```
ANY prefix length from /1 to /32:

Need 500 hosts?
  /23 = 510 hosts ✅ (Perfect fit!)

Need 1,000 hosts?
  /22 = 1,022 hosts ✅

Need 100 hosts?
  /25 = 126 hosts ✅

Need 30 hosts?
  /27 = 30 hosts ✅

Need 2 hosts (point-to-point)?
  /30 = 2 hosts ✅
```

#### 2. Efficient IP Utilization

**Example: Company Needs 1,000 IPs**

```
Classful Approach:
─────────────────────────────────────
Option 1: Use 4 Class C networks
  4 × 254 = 1,016 addresses
  Problems:
    ❌ Need 4 routing entries
    ❌ Complex management
    ❌ Non-contiguous might not get them
    ❌ Still wastes 16 IPs

Option 2: Use 1 Class B
  65,534 addresses
  Problems:
    ❌ Waste 64,534 addresses (98.5% waste!)
    ❌ Unethical during IPv4 shortage

CIDR Approach:
─────────────────────────────────────
Use /22 (10.0.0.0/22)
  1,022 addresses
  Benefits:
    ✅ Only 22 IPs wasted (2% waste)
    ✅ Single routing entry
    ✅ Easy to manage
    ✅ Perfect fit!

Efficiency Gain: 98.5% → 2% waste (49x improvement!)
```

#### 3. Route Aggregation (Supernetting)

**The Game-Changer:** Multiple networks can be combined into one routing entry.

**Classful (No Aggregation):**
```
ISP has customers with:
  200.50.0.0/24
  200.50.1.0/24
  200.50.2.0/24
  200.50.3.0/24

Routing Table:
─────────────────────────────────────
200.50.0.0/24   via ISP
200.50.1.0/24   via ISP
200.50.2.0/24   via ISP
200.50.3.0/24   via ISP

4 routing entries
```

**CIDR (With Aggregation):**
```
Same networks aggregated:

Routing Table:
─────────────────────────────────────
200.50.0.0/22   via ISP

1 routing entry (75% reduction!)
```

**Real-World Impact:**

```
Internet Routing Table Growth:

1990 (Classful): ~5,000 routes
1993 (Classful): ~50,000 routes (growing exponentially 📈)
1994 (CIDR adopted): Growth rate slowed
2000: ~90,000 routes
2010: ~320,000 routes
2020: ~850,000 routes

Without CIDR: Would have hit 1 million routes by 1995!
Router memory: Would have needed 10x more RAM
```

#### 4. Hierarchical Address Allocation

**CIDR enables provider-based addressing:**

```
Example: Regional Internet Registry (RIR)

IANA allocates to RIR:
─────────────────────────────────────
203.0.0.0/8

RIR allocates to Large ISP:
─────────────────────────────────────
203.0.0.0/16 (65,536 addresses)

Large ISP allocates to Medium ISP:
─────────────────────────────────────
203.0.128.0/18 (16,384 addresses)

Medium ISP allocates to customers:
─────────────────────────────────────
203.0.128.0/24 → Customer A (254 addresses)
203.0.129.0/24 → Customer B (254 addresses)
203.0.130.0/22 → Customer C (1,022 addresses)

Global Routing Table sees only:
─────────────────────────────────────
203.0.0.0/8 → Routes to RIR region

Massive reduction in global routing entries!
```

#### 5. VLSM (Variable Length Subnet Masking)

**Classful:** All subnets must be same size within a class.

**CIDR:** Each subnet can be different size based on actual needs.

**Example: Corporate Network Design**

```
Company Allocated: 10.50.0.0/16

Requirements using VLSM:
─────────────────────────────────────

1. Data Center (1,000 hosts)
   10.50.0.0/22 → 1,022 hosts ✅

2. Office Building 1 (500 hosts)
   10.50.4.0/23 → 510 hosts ✅

3. Office Building 2 (200 hosts)
   10.50.6.0/24 → 254 hosts ✅

4. Office Building 3 (100 hosts)
   10.50.7.0/25 → 126 hosts ✅

5. Remote Office 1 (50 hosts)
   10.50.7.128/26 → 62 hosts ✅

6. Remote Office 2 (20 hosts)
   10.50.7.192/27 → 30 hosts ✅

7. Point-to-Point Links (10 links, 2 hosts each)
   10.50.7.224/30 → Link 1 (2 hosts)
   10.50.7.228/30 → Link 2 (2 hosts)
   ... (8 more links)

Total used: ~1,900 addresses
Total available in /16: 65,534 addresses
Remaining for future growth: 63,634 addresses

With classful: Would need multiple Class B or C networks,
               impossible to design this efficiently!
```

#### 6.延长 IPv4 Lifespan

**Impact on IPv4 Exhaustion:**

```
Without CIDR:
─────────────────────────────────────
Projected IPv4 exhaustion: 1995-1996
Internet growth: Would have been severely limited
Economic impact: Catastrophic

With CIDR (Introduced 1993):
─────────────────────────────────────
Actual IPv4 exhaustion: 2011 (IANA), 2014-2015 (RIRs)
Extended lifespan: 15-20 years!
Allowed time for: IPv6 development and deployment
Economic impact: Internet boom continued
```

#### 7. Better Network Design

**CIDR Enables:**

```
✅ Right-sized subnets (no more wasted space)
✅ Hierarchical addressing (logical organization)
✅ Route summarization (smaller routing tables)
✅ Discontiguous subnets (flexible topology)
✅ Address renumbering (changing ISPs easier)
✅ Better security (precise segmentation)
```

**Example: AWS VPC Design (Impossible Without CIDR)**

```
VPC: 10.0.0.0/16

Public Subnets:
  10.0.1.0/24  (AZ-1a) → Web servers
  10.0.2.0/24  (AZ-1b) → Web servers

Private Subnets:
  10.0.10.0/23 (AZ-1a) → App servers (510 hosts)
  10.0.12.0/23 (AZ-1b) → App servers (510 hosts)

Database Subnets:
  10.0.20.0/24 (AZ-1a) → Databases
  10.0.21.0/24 (AZ-1b) → Databases

Management Subnet:
  10.0.30.0/27 (AZ-1a) → Bastion hosts (30 hosts)

This flexible design is only possible with CIDR!
```

### CIDR vs Classful: Direct Comparison

| Aspect | Classful Addressing | CIDR |
|--------|---------------------|------|
| **Prefix Options** | Only /8, /16, /24 | Any /1 to /32 |
| **Flexibility** | Rigid, 3 sizes | Infinite granularity |
| **Waste** | 80-99% typical | 1-10% typical |
| **Routing Table** | One entry per network | Aggregation possible |
| **Subnet Design** | Limited options | VLSM supported |
| **Address Allocation** | Fixed blocks | Right-sized blocks |
| **IPv4 Lifespan** | Exhaustion by 1996 | Extended to 2011 |
| **Routing Protocols** | RIPv1, IGRP | RIPv2, OSPF, EIGRP, BGP |
| **Global Routes (1993)** | 50,000+ (exponential) | Stabilized growth |
| **Hierarchical Design** | Not supported | Fully supported |
| **Provider Changes** | Difficult | Easier renumbering |

### CIDR Calculation Examples

#### Example 1: Perfect Fit

```
Requirement: 60 hosts

Classful:
  Class C: 254 hosts
  Waste: 194 addresses (76%)

CIDR:
  Calculate: 2^n - 2 ≥ 60
  n = 6: 2^6 - 2 = 62 hosts ✅
  Prefix: /26 (32 - 6 = 26)
  Subnet: 10.0.0.0/26
  Waste: 2 addresses (3%)

Efficiency gain: 76% → 3% waste (25x improvement!)
```

#### Example 2: Aggregation

```
You have 4 contiguous /24 networks:
  192.168.0.0/24
  192.168.1.0/24
  192.168.2.0/24
  192.168.3.0/24

Classful: 4 routing entries

CIDR: Can aggregate to single /22:
  192.168.0.0/22 (covers .0 through .3)
  
Routing entries: 1 (75% reduction!)

Check:
  192.168.0.0   = 11000000.10101000.00000000.00000000
  192.168.3.255 = 11000000.10101000.00000011.11111111
                                      ↑↑
  Differ only in last 10 bits, so /22 works!
```

#### Example 3: ISP Allocation

```
ISP needs to serve:
  - 50 business customers (avg 20 IPs each) = 1,000 IPs
  - 5,000 residential customers (1 IP each) = 5,000 IPs
  - Future growth = 2,000 IPs
  Total: ~8,000 IPs

Classful:
  Would need multiple Class Cs or waste a Class B

CIDR:
  Calculate: 2^n ≥ 8,000
  n = 13: 2^13 = 8,192 ✅
  Prefix: /19 (32 - 13 = 19)
  Allocation: 203.0.128.0/19
  Total IPs: 8,192
  Waste: 192 (2.3%)

Perfect fit!
```

### Why CIDR Succeeded

```
1. Saved the Internet
   ├─ Prevented IPv4 exhaustion in 1995
   ├─ Bought 15-20 years for IPv6 development
   └─ Enabled continued internet growth

2. Economic Benefits
   ├─ Reduced waste from 90% to <5%
   ├─ Lower costs for address allocation
   ├─ Enabled efficient ISP growth
   └─ Supported internet business boom

3. Technical Advantages
   ├─ Smaller routing tables (router memory savings)
   ├─ Faster routing lookups
   ├─ Better network design flexibility
   └─ Hierarchical addressing

4. Still Used Today
   ├─ All modern networks use CIDR
   ├─ IPv6 also uses CIDR principles
   ├─ Industry standard for 30+ years
   └─ Taught in all networking courses
```

---

## 🔢 CIDR Notation (Expanded)

**CIDR (Classless Inter-Domain Routing)** notation uses a slash followed by the number of network bits (prefix length).

### CIDR Format

```
IP_Address/Prefix_Length
Examples:
  192.168.1.0/24
  10.0.0.0/8
  172.16.0.0/16
  203.0.113.0/26
```

### Understanding the Prefix Length

The number after the slash indicates **how many bits** (from the left) are the network portion.

```
Example: 192.168.10.0/24

/24 means:
  - First 24 bits = Network portion
  - Remaining 8 bits (32-24) = Host portion
  - Subnet mask = 255.255.255.0

Binary visualization:
Network (24 bits)                    Host (8 bits)
↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓            ↓↓↓↓↓↓↓↓
11000000.10101000.00001010.        00000000
192     .168     .10      .        0
```

### Complete CIDR Reference Table

| CIDR | Subnet Mask | Wildcard Mask | # of IPs | Usable Hosts | Typical Use |
|------|-------------|---------------|----------|--------------|-------------|
| /1 | 128.0.0.0 | 127.255.255.255 | 2,147,483,648 | 2,147,483,646 | Never used |
| /2 | 192.0.0.0 | 63.255.255.255 | 1,073,741,824 | 1,073,741,822 | Never used |
| /3 | 224.0.0.0 | 31.255.255.255 | 536,870,912 | 536,870,910 | Regional allocation |
| /4 | 240.0.0.0 | 15.255.255.255 | 268,435,456 | 268,435,454 | Regional allocation |
| /5 | 248.0.0.0 | 7.255.255.255 | 134,217,728 | 134,217,726 | Regional allocation |
| /6 | 252.0.0.0 | 3.255.255.255 | 67,108,864 | 67,108,862 | Regional allocation |
| /7 | 254.0.0.0 | 1.255.255.255 | 33,554,432 | 33,554,430 | Large ISPs |
| /8 | 255.0.0.0 | 0.255.255.255 | 16,777,216 | 16,777,214 | Class A equivalent |
| /9 | 255.128.0.0 | 0.127.255.255 | 8,388,608 | 8,388,606 | Large ISPs |
| /10 | 255.192.0.0 | 0.63.255.255 | 4,194,304 | 4,194,302 | Large ISPs |
| /11 | 255.224.0.0 | 0.31.255.255 | 2,097,152 | 2,097,150 | ISPs |
| /12 | 255.240.0.0 | 0.15.255.255 | 1,048,576 | 1,048,574 | Large orgs |
| /13 | 255.248.0.0 | 0.7.255.255 | 524,288 | 524,286 | Large orgs |
| /14 | 255.252.0.0 | 0.3.255.255 | 262,144 | 262,142 | Large orgs |
| /15 | 255.254.0.0 | 0.1.255.255 | 131,072 | 131,070 | Large orgs |
| /16 | 255.255.0.0 | 0.0.255.255 | 65,536 | 65,534 | Class B equivalent |
| /17 | 255.255.128.0 | 0.0.127.255 | 32,768 | 32,766 | Medium orgs |
| /18 | 255.255.192.0 | 0.0.63.255 | 16,384 | 16,382 | Medium orgs |
| /19 | 255.255.224.0 | 0.0.31.255 | 8,192 | 8,190 | Medium orgs |
| /20 | 255.255.240.0 | 0.0.15.255 | 4,096 | 4,094 | Small orgs |
| /21 | 255.255.248.0 | 0.0.7.255 | 2,048 | 2,046 | Small orgs |
| /22 | 255.255.252.0 | 0.0.3.255 | 1,024 | 1,022 | Small orgs |
| /23 | 255.255.254.0 | 0.0.1.255 | 512 | 510 | Small orgs |
| /24 | 255.255.255.0 | 0.0.0.255 | 256 | 254 | Class C equivalent |
| /25 | 255.255.255.128 | 0.0.0.127 | 128 | 126 | Small subnets |
| /26 | 255.255.255.192 | 0.0.0.63 | 64 | 62 | Small subnets |
| /27 | 255.255.255.224 | 0.0.0.31 | 32 | 30 | Tiny subnets |
| /28 | 255.255.255.240 | 0.0.0.15 | 16 | 14 | Very small subnets |
| /29 | 255.255.255.248 | 0.0.0.7 | 8 | 6 | Minimal subnets |
| /30 | 255.255.255.252 | 0.0.0.3 | 4 | 2 | Point-to-point |
| /31 | 255.255.255.254 | 0.0.0.1 | 2 | 2 | Point-to-point (RFC 3021) |
| /32 | 255.255.255.255 | 0.0.0.0 | 1 | 1 | Single host |

### CIDR Calculation Formulas

```bash
# Total number of IP addresses
Total IPs = 2^(32 - prefix_length)

# Number of usable hosts (exclude network & broadcast)
Usable Hosts = 2^(32 - prefix_length) - 2

# Exceptions:
# /31: Usable = 2 (RFC 3021, no network/broadcast)
# /32: Usable = 1 (single host)

# Examples:
/24: Total = 2^(32-24) = 2^8  = 256 IPs → 254 usable
/20: Total = 2^(32-20) = 2^12 = 4,096 IPs → 4,094 usable
/30: Total = 2^(32-30) = 2^2  = 4 IPs → 2 usable
```

### Converting Between CIDR and Subnet Mask

#### Method 1: Conversion Table (Memorize Common Ones)

```
Commonly Used CIDR Prefixes:
/8  = 255.0.0.0
/16 = 255.255.0.0
/24 = 255.255.255.0
/25 = 255.255.255.128
/26 = 255.255.255.192
/27 = 255.255.255.224
/28 = 255.255.255.240
/29 = 255.255.255.248
/30 = 255.255.255.252
/32 = 255.255.255.255
```

#### Method 2: Binary Conversion

```
Example: Convert /20 to subnet mask

Step 1: Write 20 ones, then 12 zeros (to make 32 bits)
────────────────────────────────────────────────
11111111.11111111.11110000.00000000

Step 2: Convert each octet to decimal
────────────────────────────────────────────────
11111111 = 255
11111111 = 255
11110000 = 240
00000000 = 0

Result: 255.255.240.0
```

#### Method 3: Quick Mental Calculation (Last Octet)

```
For /24 to /32, only the last octet changes:

/24: 255.255.255.0   → 00000000 = 0
/25: 255.255.255.128 → 10000000 = 128
/26: 255.255.255.192 → 11000000 = 192
/27: 255.255.255.224 → 11100000 = 224
/28: 255.255.255.240 → 11110000 = 240
/29: 255.255.255.248 → 11111000 = 248
/30: 255.255.255.252 → 11111100 = 252
/31: 255.255.255.254 → 11111110 = 254
/32: 255.255.255.255 → 11111111 = 255

Pattern: Each step adds a 1 bit from left to right
128 = 2^7
192 = 128 + 64 = 2^7 + 2^6
224 = 128 + 64 + 32 = 2^7 + 2^6 + 2^5
...and so on
```

### CIDR Block Examples

#### Example 1: Basic Calculation

```
Network: 192.168.10.0/24

Calculate:
─────────────────────────────────────
Prefix: /24 (24 network bits, 8 host bits)
Subnet Mask: 255.255.255.0
Total IPs: 2^8 = 256
Usable Hosts: 256 - 2 = 254

Network Address: 192.168.10.0
First Usable: 192.168.10.1
Last Usable: 192.168.10.254
Broadcast: 192.168.10.255

IP Range: 192.168.10.0 - 192.168.10.255
```

#### Example 2: Larger Network

```
Network: 172.16.0.0/12

Calculate:
─────────────────────────────────────
Prefix: /12 (12 network bits, 20 host bits)
Subnet Mask: 255.240.0.0
Total IPs: 2^20 = 1,048,576
Usable Hosts: 1,048,574

Binary Analysis:
172.16.0.0 in binary:
10101100.00010000.00000000.00000000
            ↑
          /12 ends here

Network portion: 10101100.0001
Host portion: 0000.00000000.00000000

Network Address: 172.16.0.0
Broadcast Address:
  Network: 10101100.0001
  All host bits 1: 1111.11111111.11111111
  Result: 10101100.00011111.11111111.11111111
  = 172.31.255.255

IP Range: 172.16.0.0 - 172.31.255.255
```

#### Example 3: Determining if IP is in Network

```
Question: Is 10.50.75.100 in network 10.50.64.0/20?

Step 1: Convert /20 to subnet mask
─────────────────────────────────────
/20 = 255.255.240.0

Step 2: Apply AND to test IP
─────────────────────────────────────
IP:      10.50.75.100
Mask:    255.255.240.0

Binary:
IP:      00001010.00110010.01001011.01100100
Mask:    11111111.11111111.11110000.00000000
Result:  00001010.00110010.01000000.00000000
         = 10.50.64.0

Step 3: Compare with network address
─────────────────────────────────────
Network address of test IP: 10.50.64.0
Given network address: 10.50.64.0
Match! ✅

Answer: Yes, 10.50.75.100 is in 10.50.64.0/20
```

### Supernetting (Route Aggregation)

**Supernetting** combines multiple networks into a larger CIDR block.

#### Example: Combining Networks

```
You have:
  192.168.0.0/24
  192.168.1.0/24
  192.168.2.0/24
  192.168.3.0/24

Can you aggregate them?

Step 1: Convert to binary
─────────────────────────────────────
192.168.0.0 = 11000000.10101000.00000000.00000000
192.168.1.0 = 11000000.10101000.00000001.00000000
192.168.2.0 = 11000000.10101000.00000010.00000000
192.168.3.0 = 11000000.10101000.00000011.00000000

Step 2: Find common prefix
─────────────────────────────────────
All share: 11000000.10101000.000000 (22 bits)
           Differ in bits: 23-24

Step 3: Aggregated network
─────────────────────────────────────
Network: 192.168.0.0/22
Covers: 192.168.0.0 - 192.168.3.255
Total IPs: 1,024 (4 × 256)

Routing: 1 entry instead of 4! ✅
```

#### Requirements for Aggregation

```
Networks can be aggregated if:
1. They are contiguous (sequential)
2. The number of networks is a power of 2
3. The first network starts on a boundary

Example - Can aggregate:
✅ 10.0.0.0/24, 10.0.1.0/24 → 10.0.0.0/23
✅ 10.0.0.0/24, 10.0.1.0/24, 10.0.2.0/24, 10.0.3.0/24 → 10.0.0.0/22

Example - Cannot aggregate:
❌ 10.0.0.0/24, 10.0.2.0/24 (not contiguous, missing .1)
❌ 10.0.1.0/24, 10.0.2.0/24 (doesn't start on boundary)
```

### Subnetting with CIDR

#### Example: Divide /24 into 4 Equal Subnets

```
Original: 192.168.100.0/24 (256 IPs)
Goal: 4 equal subnets

Step 1: Calculate new prefix
─────────────────────────────────────
Need 4 subnets: 2^n = 4 → n = 2
New prefix: /24 + 2 = /26

Step 2: Each subnet has
─────────────────────────────────────
IPs per subnet: 256 ÷ 4 = 64
Usable: 64 - 2 = 62 hosts

Step 3: List subnets
─────────────────────────────────────
Subnet 1: 192.168.100.0/26   (.0   - .63)
Subnet 2: 192.168.100.64/26  (.64  - .127)
Subnet 3: 192.168.100.128/26 (.128 - .191)
Subnet 4: 192.168.100.192/26 (.192 - .255)
```

#### Example: Variable-Size Subnets (VLSM)

```
Network: 10.0.0.0/16
Requirements:
  - Subnet A: 2000 hosts
  - Subnet B: 500 hosts
  - Subnet C: 100 hosts
  - Subnet D: 50 hosts

Solution (allocate largest first):
─────────────────────────────────────

Subnet A (2000 hosts):
  Need: 2^n - 2 ≥ 2000 → n = 11 (2046 hosts)
  Prefix: /21 (32 - 11 = 21)
  Network: 10.0.0.0/21
  Range: 10.0.0.0 - 10.0.7.255

Subnet B (500 hosts):
  Need: 2^n - 2 ≥ 500 → n = 9 (510 hosts)
  Prefix: /23
  Network: 10.0.8.0/23
  Range: 10.0.8.0 - 10.0.9.255

Subnet C (100 hosts):
  Need: 2^n - 2 ≥ 100 → n = 7 (126 hosts)
  Prefix: /25
  Network: 10.0.10.0/25
  Range: 10.0.10.0 - 10.0.10.127

Subnet D (50 hosts):
  Need: 2^n - 2 ≥ 50 → n = 6 (62 hosts)
  Prefix: /26
  Network: 10.0.10.128/26
  Range: 10.0.10.128 - 10.0.10.191
```

### CIDR and Private IP Ranges

```
Private IP ranges in CIDR notation (RFC 1918):

Class A:  10.0.0.0/8
  - Range: 10.0.0.0 - 10.255.255.255
  - IPs: 16,777,216
  - Use: Large private networks, AWS VPCs

Class B:  172.16.0.0/12
  - Range: 172.16.0.0 - 172.31.255.255
  - IPs: 1,048,576
  - Use: Medium private networks

Class C:  192.168.0.0/16
  - Range: 192.168.0.0 - 192.168.255.255
  - IPs: 65,536
  - Use: Home/small office networks
```

### Common CIDR Mistakes

#### Mistake 1: Wrong Network Boundary

```
❌ Incorrect:
Network: 192.168.10.5/24

Problem: Network address should be .0, not .5
✅ Correct: 192.168.10.0/24
```

#### Mistake 2: Overlapping Subnets

```
❌ Incorrect:
Subnet 1: 10.0.0.0/24 (10.0.0.0 - 10.0.0.255)
Subnet 2: 10.0.0.128/25 (10.0.0.128 - 10.0.0.255)

Problem: Overlap! .128-.255 in both subnets

✅ Correct:
Subnet 1: 10.0.0.0/25 (10.0.0.0 - 10.0.0.127)
Subnet 2: 10.0.0.128/25 (10.0.0.128 - 10.0.0.255)
```

#### Mistake 3: Misunderstanding /32

```
❌ Incorrect thinking:
"10.0.0.5/32 is a network with 1 host"

✅ Correct:
"10.0.0.5/32 is a SPECIFIC host (host route)"
Use case: Static routes, firewall rules, loopbacks
```

### Tools for CIDR Calculations

```bash
# Online calculators:
https://www.subnet-calculator.com/
https://www.ipaddressguide.com/cidr
https://cidr.xyz/

# Linux command-line:
ipcalc 192.168.10.0/24
sipcalc 10.0.0.0/22

# Python:
import ipaddress
net = ipaddress.ip_network('192.168.10.0/24')
print(f"Network: {net.network_address}")
print(f"Broadcast: {net.broadcast_address}")
print(f"Hosts: {net.num_addresses}")
for ip in net.hosts():
    print(ip)  # Lists all usable IPs
```

### CIDR Quick Reference Cheat Sheet

```
Remember these common sizes:
────────────────────────────────────────────────
/8  = 16 million IPs  (Class A)
/16 = 65 thousand IPs (Class B)
/24 = 256 IPs         (Class C - typical LAN)
/25 = 128 IPs         (small office)
/26 = 64 IPs          (smaller subnet)
/27 = 32 IPs          (tiny subnet)
/28 = 16 IPs          (very small)
/29 = 8 IPs           (minimal)
/30 = 4 IPs (2 usable - point-to-point)
/32 = 1 IP  (single host)

Powers of 2 (for quick calculation):
────────────────────────────────────────────────
2^8  = 256
2^9  = 512
2^10 = 1,024  (~1K)
2^12 = 4,096  (~4K)
2^16 = 65,536 (~64K)
2^20 = 1,048,576 (~1M)
2^24 = 16,777,216 (~16M)
```

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

## 🎭 Subnet Mask (In-Depth)

A **subnet mask** is a 32-bit number that separates the network portion from the host portion of an IP address. It's fundamental to IP networking and routing.

### What is a Subnet Mask?

The subnet mask works alongside an IP address to:
1. **Identify which network** a device belongs to
2. **Determine the host ID** within that network
3. **Enable routers to make forwarding decisions**
4. **Create logical network boundaries**

Think of it like a postal address:
- **Network portion** = City/State (identifies the location)
- **Host portion** = Street address (identifies specific building)

### How Subnet Masks Work

The subnet mask is a 32-bit number with:
- **Consecutive 1s** (left side) indicating the **network part**
- **Consecutive 0s** (right side) indicating the **host part**

**Critical Rules:**
- Must be contiguous 1s followed by contiguous 0s
- ❌ Invalid: `11110000.11110000.00001111.00000000` (non-contiguous)
- ✅ Valid: `11111111.11111111.11110000.00000000` (contiguous)

### Binary Representation

```
Subnet Mask: 255.255.255.0

Decimal:     255      .  255      .  255      .    0
             ↓            ↓            ↓           ↓
Binary:   11111111   . 11111111  . 11111111  . 00000000
          └────────────────┬────────────────┘   └───┬───┘
                    Network Bits (24)          Host Bits (8)
```

### How Bitwise AND Works

The subnet mask uses **bitwise AND** operation to extract the network address:

**Truth Table:**
```
IP Bit  Mask Bit  Result
  0       0         0
  0       1         0
  1       0         0
  1       1         1
```

**Step-by-Step Example:**

```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
Goal: Find the network address

Step 1: Convert to Binary
─────────────────────────────────────────────────────────────
IP:    192        168        1          100
       11000000 . 10101000 . 00000001 . 01100100

Mask:  255        255        255        0
       11111111 . 11111111 . 11111111 . 00000000

Step 2: Apply AND Operation (bit by bit)
─────────────────────────────────────────────────────────────
IP:    11000000 . 10101000 . 00000001 . 01100100
Mask:  11111111 . 11111111 . 11111111 . 00000000
AND:   ─────────────────────────────────────────────
Net:   11000000 . 10101000 . 00000001 . 00000000

Step 3: Convert Back to Decimal
─────────────────────────────────────────────────────────────
Network Address: 192.168.1.0
```

### Common Subnet Masks (Complete Reference)

| CIDR | Subnet Mask | Binary | Network Bits | Host Bits | # of Addresses | # Usable Hosts | Use Case |
|------|-------------|--------|--------------|-----------|----------------|----------------|----------|
| /8 | 255.0.0.0 | `11111111.00000000.00000000.00000000` | 8 | 24 | 16,777,216 | 16,777,214 | Class A, Very large ISPs |
| /9 | 255.128.0.0 | `11111111.10000000.00000000.00000000` | 9 | 23 | 8,388,608 | 8,388,606 | Large ISPs |
| /10 | 255.192.0.0 | `11111111.11000000.00000000.00000000` | 10 | 22 | 4,194,304 | 4,194,302 | Large ISPs |
| /11 | 255.224.0.0 | `11111111.11100000.00000000.00000000` | 11 | 21 | 2,097,152 | 2,097,150 | ISPs |
| /12 | 255.240.0.0 | `11111111.11110000.00000000.00000000` | 12 | 20 | 1,048,576 | 1,048,574 | ISPs, Private range |
| /13 | 255.248.0.0 | `11111111.11111000.00000000.00000000` | 13 | 19 | 524,288 | 524,286 | Large organizations |
| /14 | 255.252.0.0 | `11111111.11111100.00000000.00000000` | 14 | 18 | 262,144 | 262,142 | Large organizations |
| /15 | 255.254.0.0 | `11111111.11111110.00000000.00000000` | 15 | 17 | 131,072 | 131,070 | Large organizations |
| /16 | 255.255.0.0 | `11111111.11111111.00000000.00000000` | 16 | 16 | 65,536 | 65,534 | Class B, Medium orgs |
| /17 | 255.255.128.0 | `11111111.11111111.10000000.00000000` | 17 | 15 | 32,768 | 32,766 | Medium organizations |
| /18 | 255.255.192.0 | `11111111.11111111.11000000.00000000` | 18 | 14 | 16,384 | 16,382 | Medium organizations |
| /19 | 255.255.224.0 | `11111111.11111111.11100000.00000000` | 19 | 13 | 8,192 | 8,190 | Medium organizations |
| /20 | 255.255.240.0 | `11111111.11111111.11110000.00000000` | 20 | 12 | 4,096 | 4,094 | Small organizations |
| /21 | 255.255.248.0 | `11111111.11111111.11111000.00000000` | 21 | 11 | 2,048 | 2,046 | Small organizations |
| /22 | 255.255.252.0 | `11111111.11111111.11111100.00000000` | 22 | 10 | 1,024 | 1,022 | Small organizations |
| /23 | 255.255.254.0 | `11111111.11111111.11111110.00000000` | 23 | 9 | 512 | 510 | Small organizations |
| /24 | 255.255.255.0 | `11111111.11111111.11111111.00000000` | 24 | 8 | 256 | 254 | Class C, Typical LAN |
| /25 | 255.255.255.128 | `11111111.11111111.11111111.10000000` | 25 | 7 | 128 | 126 | Small subnet |
| /26 | 255.255.255.192 | `11111111.11111111.11111111.11000000` | 26 | 6 | 64 | 62 | Small subnet |
| /27 | 255.255.255.224 | `11111111.11111111.11111111.11100000` | 27 | 5 | 32 | 30 | Tiny subnet |
| /28 | 255.255.255.240 | `11111111.11111111.11111111.11110000` | 28 | 4 | 16 | 14 | Very small subnet |
| /29 | 255.255.255.248 | `11111111.11111111.11111111.11111000` | 29 | 3 | 8 | 6 | Minimal subnet |
| /30 | 255.255.255.252 | `11111111.11111111.11111111.11111100` | 30 | 2 | 4 | 2 | Point-to-point links |
| /31 | 255.255.255.254 | `11111111.11111111.11111111.11111110` | 31 | 1 | 2 | 2 | Point-to-point (RFC 3021) |
| /32 | 255.255.255.255 | `11111111.11111111.11111111.11111111` | 32 | 0 | 1 | 1 | Single host route |

### Calculating Network Components

For any given IP address and subnet mask, you can calculate:

#### 1. Network Address
```
IP AND Subnet Mask = Network Address

Example: 172.16.50.100/20
IP:       172.16.50.100     → 10101100.00010000.00110010.01100100
Mask:     255.255.240.0     → 11111111.11111111.11110000.00000000
Network:  172.16.48.0       → 10101100.00010000.00110000.00000000
```

#### 2. Broadcast Address
```
Network Address OR (NOT Subnet Mask) = Broadcast Address

Example: 172.16.50.100/20
Network:     172.16.48.0    → 10101100.00010000.00110000.00000000
NOT Mask:    0.0.15.255     → 00000000.00000000.00001111.11111111
Broadcast:   172.16.63.255  → 10101100.00010000.00111111.11111111
```

#### 3. First Usable Host
```
Network Address + 1

Example: 172.16.48.0/20
First Host: 172.16.48.1
```

#### 4. Last Usable Host
```
Broadcast Address - 1

Example: 172.16.63.255/20
Last Host: 172.16.63.254
```

#### 5. Total Number of Addresses
```
2^(32 - prefix length)

Example: /20
Total = 2^(32-20) = 2^12 = 4,096 addresses
```

#### 6. Number of Usable Hosts
```
2^(32 - prefix length) - 2

Example: /20
Usable = 2^12 - 2 = 4,094 hosts
(Subtract 2 for network address and broadcast address)
```

### Advanced Subnet Mask Examples

#### Example 1: Class C Subnetting

**Scenario:** Subnet `192.168.10.0/24` into 4 equal subnets

```
Original: 192.168.10.0/24 (254 hosts)

Step 1: Calculate new prefix
─────────────────────────────────────
Need 4 subnets: 2^n ≥ 4 → n = 2 bits
New prefix: /24 + 2 = /26

Step 2: New subnet mask
─────────────────────────────────────
/26 = 255.255.255.192

Step 3: Calculate subnet increment
─────────────────────────────────────
256 - 192 = 64 (block size)

Step 4: List all subnets
─────────────────────────────────────
Subnet 1: 192.168.10.0/26
  Network:    192.168.10.0
  First Host: 192.168.10.1
  Last Host:  192.168.10.62
  Broadcast:  192.168.10.63
  Hosts:      62

Subnet 2: 192.168.10.64/26
  Network:    192.168.10.64
  First Host: 192.168.10.65
  Last Host:  192.168.10.126
  Broadcast:  192.168.10.127
  Hosts:      62

Subnet 3: 192.168.10.128/26
  Network:    192.168.10.128
  First Host: 192.168.10.129
  Last Host:  192.168.10.190
  Broadcast:  192.168.10.191
  Hosts:      62

Subnet 4: 192.168.10.192/26
  Network:    192.168.10.192
  First Host: 192.168.10.193
  Last Host:  192.168.10.254
  Broadcast:  192.168.10.255
  Hosts:      62
```

#### Example 2: Class B Subnetting

**Scenario:** Subnet `172.20.0.0/16` to support 100 subnets with ~500 hosts each

```
Step 1: Calculate host bits needed
─────────────────────────────────────
Need 500 hosts: 2^n - 2 ≥ 500 → n = 9 bits
(2^9 - 2 = 510 hosts)

Step 2: Calculate network bits needed
─────────────────────────────────────
Need 100 subnets: 2^n ≥ 100 → n = 7 bits
(2^7 = 128 subnets)

Step 3: Verify: 16 (original) + 7 (subnet bits) + 9 (host bits) = 32 ✓

Step 4: New prefix
─────────────────────────────────────
/16 + 7 = /23

Step 5: New subnet mask
─────────────────────────────────────
/23 = 255.255.254.0

Step 6: Sample subnets
─────────────────────────────────────
Subnet 1:  172.20.0.0/23    (172.20.0.0   - 172.20.1.255)
Subnet 2:  172.20.2.0/23    (172.20.2.0   - 172.20.3.255)
Subnet 3:  172.20.4.0/23    (172.20.4.0   - 172.20.5.255)
...
Subnet 100: 172.20.198.0/23 (172.20.198.0 - 172.20.199.255)
```

#### Example 3: VLSM (Variable Length Subnet Masking)

**Scenario:** Allocate subnets from `10.0.0.0/16` for:
- **Dept A:** 4000 hosts
- **Dept B:** 2000 hosts
- **Dept C:** 500 hosts
- **Point-to-point link:** 2 hosts

```
Start with largest requirement first:

1. Dept A (4000 hosts)
   ─────────────────────────────────────
   Need: 2^n - 2 ≥ 4000 → n = 12 bits
   Prefix: /20 (32 - 12 = 20)
   Subnet: 10.0.0.0/20
   Range: 10.0.0.0 - 10.0.15.255
   Usable: 4094 hosts

2. Dept B (2000 hosts)
   ─────────────────────────────────────
   Need: 2^n - 2 ≥ 2000 → n = 11 bits
   Prefix: /21
   Subnet: 10.0.16.0/21
   Range: 10.0.16.0 - 10.0.23.255
   Usable: 2046 hosts

3. Dept C (500 hosts)
   ─────────────────────────────────────
   Need: 2^n - 2 ≥ 500 → n = 9 bits
   Prefix: /23
   Subnet: 10.0.24.0/23
   Range: 10.0.24.0 - 10.0.25.255
   Usable: 510 hosts

4. Point-to-Point Link (2 hosts)
   ─────────────────────────────────────
   Need: 2 hosts exactly
   Prefix: /30
   Subnet: 10.0.26.0/30
   Range: 10.0.26.0 - 10.0.26.3
   Usable: 2 hosts
```

### Special Subnet Masks

#### /31 Subnet (RFC 3021)
- **Use Case:** Point-to-point links
- **Addresses:** 2 (both usable, no network/broadcast)
- **Example:** `10.0.0.0/31` → `10.0.0.0` and `10.0.0.1` both usable
- **Benefit:** Saves 2 IPs per link compared to /30

#### /32 Subnet
- **Use Case:** Host routes, loopback, static routes
- **Addresses:** 1 (single specific host)
- **Example:** `8.8.8.8/32` → Only `8.8.8.8` itself

### Subnet Mask in Routing

Routers use subnet masks to determine:

1. **Same Network?**
```
Host A: 192.168.10.50/24
Host B: 192.168.10.100/24

Both have network address 192.168.10.0
→ Same network, direct communication
```

2. **Different Network?**
```
Host A: 192.168.10.50/24  (Network: 192.168.10.0)
Host C: 192.168.20.50/24  (Network: 192.168.20.0)

→ Different networks, need router
```

3. **Supernet Check:**
```
With /23 mask:
192.168.10.50/23  (Network: 192.168.10.0)
192.168.11.50/23  (Network: 192.168.10.0)

→ Same network! (Supernetting combines networks)
```

### Common Subnet Mask Errors

| Error | Problem | Impact | Solution |
|-------|---------|--------|----------|
| Non-contiguous mask | `255.255.0.255` | Invalid, unpredictable routing | Use contiguous: `255.255.255.0` |
| Overlapping subnets | Two /24s from same /23 | IP conflicts, routing loops | Plan subnet allocation carefully |
| Too small subnet | /30 for 5 hosts | Not enough IPs | Use /29 (6 hosts) or /28 (14 hosts) |
| Wrong subnet assignment | Host: `10.0.0.5/32` | Isolated, can't communicate | Use proper mask: `10.0.0.5/24` |

### Subnet Mask Quick Reference Formulas

```bash
# Number of subnets created
Subnets = 2^(borrowed bits)

# Number of total addresses per subnet
Total Addresses = 2^(host bits)

# Number of usable hosts per subnet
Usable Hosts = 2^(host bits) - 2

# Subnet increment (block size)
Increment = 256 - (last octet of subnet mask)

# Network address
Network = IP AND Subnet Mask

# Broadcast address
Broadcast = Network + (Increment - 1)

# First usable host
First Host = Network + 1

# Last usable host
Last Host = Broadcast - 1
```

### Practical Tools for Subnet Calculations

```bash
# Linux: Calculate subnet with ipcalc
ipcalc 192.168.10.0/24

# Output:
# Address:   192.168.10.0
# Netmask:   255.255.255.0 = 24
# Wildcard:  0.0.0.255
# Network:   192.168.10.0/24
# HostMin:   192.168.10.1
# HostMax:   192.168.10.254
# Broadcast: 192.168.10.255
# Hosts/Net: 254

# Linux: IP command
ip addr show
ip route show

# Check if IP is in subnet (Python)
python3 -c "import ipaddress; print('192.168.10.50' in ipaddress.ip_network('192.168.10.0/24'))"
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
