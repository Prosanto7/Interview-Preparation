# ☁️ AWS (Amazon Web Services) Guide

> Comprehensive guide to AWS services - from fundamentals to advanced concepts.

---

## 📚 Table of Contents

1. [VPC (Virtual Private Cloud)](#-vpc-virtual-private-cloud)
2. [Subnets](#-subnets)
3. [Route Tables](#-route-tables)
4. [Internet Gateway](#-internet-gateway)
5. [Security Groups](#-security-groups)
6. [EC2 (Elastic Compute Cloud)](#-ec2-elastic-compute-cloud)
7. [Public vs Private EC2](#-public-vs-private-ec2)
8. [Elastic IP](#-elastic-ip)
9. [RDS (Relational Database Service)](#-rds-relational-database-service)
10. [Private RDS](#-private-rds)
11. [S3 (Simple Storage Service)](#-s3-simple-storage-service)
12. [IAM (Identity and Access Management)](#-iam-identity-and-access-management)
13. [References](#-references)

---

## 🌐 VPC (Virtual Private Cloud)

### What is VPC?

**Amazon VPC** is your own **isolated private network** within AWS cloud. Think of it as your own data center in the cloud, where you have complete control over:

- IP address ranges
- Subnets
- Route tables
- Network gateways

### Why Use VPC?

| Benefit | Description |
|---------|-------------|
| **Isolation** | Your resources are isolated from other AWS customers |
| **Security** | Control inbound and outbound traffic |
| **Customization** | Define your own network topology |
| **Connectivity** | Connect to on-premises networks via VPN or Direct Connect |

### VPC Components

```
┌─────────────────────────────────────────────────────────────┐
│                         VPC (10.0.0.0/16)                   │
│  ┌──────────────────────┐    ┌──────────────────────┐      │
│  │  Public Subnet       │    │  Private Subnet      │      │
│  │  10.0.1.0/24         │    │  10.0.2.0/24         │      │
│  │  ┌────────────┐      │    │  ┌────────────┐     │      │
│  │  │ EC2 (Web)  │      │    │  │ RDS        │     │      │
│  │  │ Public IP  │      │    │  │ Private IP │     │      │
│  │  └────────────┘      │    │  └────────────┘     │      │
│  └──────────────────────┘    └──────────────────────┘      │
│           │                                                  │
│    ┌──────▼────────┐                                        │
│    │ Internet      │                                        │
│    │ Gateway       │                                        │
│    └───────────────┘                                        │
└─────────────────────────────────────────────────────────────┘
```

### Creating a VPC

**Key Parameters:**

| Parameter | Example | Description |
|-----------|---------|-------------|
| **CIDR Block** | `10.0.0.0/16` | IP range for your VPC (65,536 IPs) |
| **Tenancy** | Default | Shared hardware (cheaper) |
| **DNS Hostname** | Enabled | Assign DNS names to instances |
| **DNS Resolution** | Enabled | Use AWS DNS server |

**Example:**
```bash
# AWS CLI
aws ec2 create-vpc --cidr-block 10.0.0.0/16
```

### Default VPC vs Custom VPC

| Feature | Default VPC | Custom VPC |
|---------|-------------|------------|
| **CIDR** | `172.31.0.0/16` | Your choice |
| **Subnets** | One per AZ (public) | You create |
| **Internet Gateway** | Attached | You attach |
| **Route Table** | Routes to IGW | You configure |
| **Use Case** | Quick start, testing | Production, compliance |

### Best Practices

✅ **Do:**
- Use private subnets for databases and application servers
- Use /16 CIDR for VPC, /24 for subnets
- Plan IP addressing before creating resources
- Use multiple Availability Zones for high availability
- Enable VPC Flow Logs for monitoring

❌ **Don't:**
- Use overlapping CIDR blocks if you plan to peer VPCs
- Make everything public
- Forget to plan for growth

---

## 📦 Subnets

### What is a Subnet?

A **subnet** is a segment of your VPC's IP range where you place AWS resources. It exists in a **single Availability Zone**.

### Public vs Private Subnet

| Type | Internet Access | Route to IGW | Use Case | Example IPs |
|------|----------------|--------------|----------|-------------|
| **Public** | Yes (via IGW) | ✅ Yes | Web servers, load balancers | Auto-assigned public IPs |
| **Private** | No direct access | ❌ No | Databases, app servers | Private IPs only |

### Subnet Architecture Example

```
VPC: 10.0.0.0/16

Availability Zone A:
├── Public Subnet A:   10.0.1.0/24  (256 IPs)
└── Private Subnet A:  10.0.2.0/24  (256 IPs)

Availability Zone B:
├── Public Subnet B:   10.0.3.0/24  (256 IPs)
└── Private Subnet B:  10.0.4.0/24  (256 IPs)
```

### Creating Subnets

**Key Parameters:**

| Parameter | Example | Description |
|-----------|---------|-------------|
| **VPC ID** | `vpc-abc123` | Parent VPC |
| **CIDR Block** | `10.0.1.0/24` | Subnet IP range |
| **Availability Zone** | `us-east-1a` | Physical location |
| **Public IP** | Enable/Disable | Auto-assign public IPs |

**Example:**
```bash
# Create public subnet
aws ec2 create-subnet \
  --vpc-id vpc-abc123 \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a

# Enable auto-assign public IP
aws ec2 modify-subnet-attribute \
  --subnet-id subnet-xyz789 \
  --map-public-ip-on-launch
```

### Reserved IPs in Subnet

AWS reserves **5 IP addresses** in each subnet:

**Example: 10.0.1.0/24 (256 total IPs)**

| IP | Purpose |
|----|---------|
| `10.0.1.0` | Network address |
| `10.0.1.1` | VPC router |
| `10.0.1.2` | DNS server |
| `10.0.1.3` | Reserved for future use |
| `10.0.1.255` | Broadcast address |

**Usable IPs:** 256 - 5 = **251 IPs**

---

## 🗺️ Route Tables

### What is a Route Table?

A **route table** contains rules (routes) that determine where network traffic is directed.

### Main vs Custom Route Table

| Type | Description |
|------|-------------|
| **Main Route Table** | Automatically created with VPC, applies to all subnets without explicit association |
| **Custom Route Table** | Created manually, explicitly associated with subnets |

### Route Table Structure

Each route consists of:

| Component | Description | Example |
|-----------|-------------|---------|
| **Destination** | Target CIDR block | `0.0.0.0/0` (all traffic) |
| **Target** | Where to send traffic | `igw-abc123` (Internet Gateway) |

### Example Route Tables

**Public Subnet Route Table:**
```
Destination       Target              Description
10.0.0.0/16      local               VPC internal traffic
0.0.0.0/0        igw-abc123          Internet-bound traffic
```

**Private Subnet Route Table:**
```
Destination       Target              Description
10.0.0.0/16      local               VPC internal traffic only
```

**Private Subnet with NAT (for internet access):**
```
Destination       Target              Description
10.0.0.0/16      local               VPC internal traffic
0.0.0.0/0        nat-xyz789          Internet via NAT Gateway
```

### Creating and Associating Route Tables

```bash
# Create route table
aws ec2 create-route-table --vpc-id vpc-abc123

# Add route to Internet Gateway
aws ec2 create-route \
  --route-table-id rtb-abc123 \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id igw-xyz789

# Associate with subnet
aws ec2 associate-route-table \
  --route-table-id rtb-abc123 \
  --subnet-id subnet-def456
```

---

## 🌍 Internet Gateway

### What is an Internet Gateway (IGW)?

An **Internet Gateway** is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet.

### Key Characteristics

- **One IGW per VPC** (1:1 relationship)
- **Highly available** - No single point of failure
- **No bandwidth constraints** - Scales automatically
- **Free** - No additional charge

### How IGW Works

```
Internet
   ↕
[Internet Gateway] ← Attached to VPC
   ↕
[Route Table] ← Routes 0.0.0.0/0 to IGW
   ↕
[Public Subnet]
   ↕
[EC2 with Public IP]
```

### Requirements for Internet Access

For an EC2 instance to access the internet, you need **ALL** of these:

1. ✅ Instance in a **public subnet**
2. ✅ **Public IP** or Elastic IP assigned
3. ✅ **Internet Gateway** attached to VPC
4. ✅ **Route table** with route to IGW (`0.0.0.0/0 → igw-xxx`)
5. ✅ **Security Group** allows outbound traffic
6. ✅ **Network ACL** allows traffic

### Creating and Attaching IGW

```bash
# Create Internet Gateway
aws ec2 create-internet-gateway

# Attach to VPC
aws ec2 attach-internet-gateway \
  --internet-gateway-id igw-abc123 \
  --vpc-id vpc-xyz789
```

### NAT Gateway vs Internet Gateway

| Feature | Internet Gateway | NAT Gateway |
|---------|------------------|-------------|
| **Purpose** | Two-way internet access | One-way (outbound only) |
| **Used By** | Public subnets | Private subnets |
| **Inbound** | Allowed | Blocked |
| **Outbound** | Allowed | Allowed |
| **Cost** | Free | Hourly + data transfer charges |
| **Location** | VPC-level | Subnet-level (one per AZ recommended) |

---

## 🔒 Security Groups

### What is a Security Group?

A **Security Group** acts as a **virtual firewall** at the instance level, controlling inbound and outbound traffic.

### Key Characteristics

| Feature | Description |
|---------|-------------|
| **Stateful** | Return traffic automatically allowed |
| **Default Deny** | Everything blocked unless explicitly allowed |
| **Instance Level** | Applied to ENI (Elastic Network Interface) |
| **Multiple SGs** | Can assign up to 5 per instance |
| **VPC Specific** | Cannot span VPCs |

### Security Group Rules

Each rule consists of:

| Component | Description | Example |
|-----------|-------------|---------|
| **Type** | Protocol | HTTP, HTTPS, SSH, Custom |
| **Protocol** | Transport protocol | TCP, UDP, ICMP |
| **Port Range** | Port or range | 80, 443, 3000-3010 |
| **Source/Destination** | CIDR, IP, or another SG | `0.0.0.0/0`, `sg-abc123` |
| **Description** | Optional label | "Allow web traffic" |

### Common Security Group Examples

**Web Server Security Group:**
```
Inbound Rules:
Type      Protocol  Port    Source          Description
HTTP      TCP       80      0.0.0.0/0       Allow web traffic
HTTPS     TCP       443     0.0.0.0/0       Allow secure web
SSH       TCP       22      203.0.113.0/24  Admin access only

Outbound Rules:
Type      Protocol  Port    Destination     Description
All       All       All     0.0.0.0/0       Allow all outbound
```

**Database Security Group:**
```
Inbound Rules:
Type      Protocol  Port    Source          Description
MySQL     TCP       3306    sg-web-servers  From web servers only
Custom    TCP       5432    sg-app-servers  PostgreSQL from apps

Outbound Rules:
Type      Protocol  Port    Destination     Description
All       All       All     0.0.0.0/0       Allow all outbound
```

### Security Groups vs Network ACLs

| Feature | Security Group | Network ACL |
|---------|---------------|-------------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow and Deny |
| **Rule Evaluation** | All rules | Numbered order |
| **Applies To** | Instances with SG assigned | All instances in subnet |
| **Default** | Deny all inbound, allow all outbound | Allow all |

### Best Practices

✅ **Do:**
- Use least privilege principle
- Reference other security groups instead of IPs when possible
- Use descriptive names and descriptions
- Create separate SGs for different tiers (web, app, database)
- Restrict SSH/RDP to specific IPs

❌ **Don't:**
- Allow `0.0.0.0/0` on SSH (port 22) or RDP (port 3389)
- Use overly permissive rules
- Forget to review and audit regularly

---

## 🧩 EC2 (Elastic Compute Cloud)

**Amazon Elastic Compute Cloud (EC2)** is a web service that provides **resizable compute capacity** in the cloud.
You can think of it as **renting virtual computers** on which you can run your own applications, just like physical servers — but **on-demand, scalable, and pay-as-you-go**.

### Key Concepts:

* **Elastic** → You can easily scale up or down the number of servers.
* **Compute** → It’s all about running programs and processing data.
* **Cloud** → You don’t need to own or manage physical hardware.

---

## ⚙️ Core Components of EC2

### 1. **Instances**

An **instance** is a virtual server in AWS.
You can choose the hardware configuration, OS, networking, and storage.

Example:

* `t3.micro` → Low-cost, burstable general-purpose instance.
* `m5.large` → Balanced compute and memory.
* `p3.2xlarge` → GPU-accelerated for AI/ML workloads.

---

### 2. **Amazon Machine Image (AMI)**

An **AMI** is a **template** used to create EC2 instances.
It includes:

* The operating system (e.g., Ubuntu, Amazon Linux, Windows)
* Application server
* System settings and software

You can use:

* AWS-provided AMIs (e.g., Amazon Linux 2)
* Marketplace AMIs (third-party vendors)
* Your own custom AMIs (snapshots of configured servers)

---

### 3. **Instance Types**

Instance types define **hardware specifications** like:

* vCPUs
* Memory (RAM)
* Storage
* Network performance

#### Common Families:

| Family    | Purpose                 | Example                  |
| --------- | ----------------------- | ------------------------ |
| **t**     | General purpose (cheap) | `t3.micro`, `t3a.medium` |
| **m**     | Balanced (CPU + RAM)    | `m5.large`               |
| **c**     | Compute-optimized       | `c6g.large`              |
| **r**     | Memory-optimized        | `r5.xlarge`              |
| **p/g**   | GPU-optimized           | `p3`, `g5`               |
| **i/d/h** | Storage-optimized       | `i3`, `d2`, `h1`         |

---

### 4. **EBS (Elastic Block Store)**

EBS provides **persistent block storage** for EC2 instances.

* Think of it like a hard drive attached to your virtual machine.
* Data persists even after you stop or terminate the instance (if configured).
* You can create **snapshots** for backup and recovery.

Types of EBS volumes:

* General Purpose SSD (gp3)
* Provisioned IOPS SSD (io2)
* Throughput Optimized HDD (st1)
* Cold HDD (sc1)

---

### 5. **Security Groups**

A **Security Group** acts as a **virtual firewall** for your instance.

* Controls inbound and outbound traffic based on **protocol**, **port**, and **IP address**.
* Example: Allow SSH (port 22) only from your office IP.

---

### 6. **Key Pairs**

Used for **authentication** when connecting via SSH (Linux) or RDP (Windows).

* AWS stores the public key.
* You download the private key (`.pem` file) and use it to access the instance securely.

---

### 7. **Elastic IP**

A **static public IPv4 address** that you can assign to an EC2 instance.
Unlike regular public IPs, Elastic IPs don’t change when you stop/start instances.

---

### 8. **Instance Lifecycle**

| State        | Description                     |
| ------------ | ------------------------------- |
| `pending`    | Being launched                  |
| `running`    | Active and usable               |
| `stopping`   | Shutting down                   |
| `stopped`    | Shut down, but can be restarted |
| `terminated` | Permanently deleted             |

---

## 🧠 Advanced Features

### 1. **Auto Scaling**

Automatically increase or decrease the number of instances based on:

* CPU usage
* Request load
* Custom CloudWatch metrics

Ensures performance while minimizing cost.

---

### 2. **Elastic Load Balancing (ELB)**

Distributes incoming traffic across multiple EC2 instances for:

* High availability
* Fault tolerance
* Better performance

---

### 3. **Placement Groups**

Control how instances are placed across hardware:

* **Cluster**: Low latency between instances.
* **Spread**: High availability — placed on separate hardware.
* **Partition**: For big data workloads — isolates failure domains.

---

### 4. **Spot Instances**

Unused EC2 capacity sold at a **discount (up to 90% cheaper)**, but can be **interrupted** anytime by AWS.
Used for batch processing, data analysis, or non-critical workloads.

---

### 5. **Reserved Instances**

Commit to using EC2 for **1 or 3 years** to get up to **75% discount** compared to on-demand pricing.

---

### 6. **Savings Plans**

More flexible than Reserved Instances — commit to a certain **dollar amount per hour** instead of specific instance types.

---

## 🔒 Security and IAM Integration

* **IAM Roles** can be attached to EC2 instances to grant temporary permissions.
* Example: Allow an instance to upload files to an S3 bucket securely **without storing credentials**.

---

## 💰 Pricing Models

| Model             | Description         | Best For                              |
| ----------------- | ------------------- | ------------------------------------- |
| **On-Demand**     | Pay per second/hour | Short-term or unpredictable workloads |
| **Reserved**      | Commit 1–3 years    | Steady-state workloads                |
| **Spot**          | Use spare capacity  | Flexible, fault-tolerant workloads    |
| **Savings Plans** | Commit to $/hour    | Flexible usage patterns               |

---

## 🧑‍💻 Common Use Cases

1. **Web Hosting** – Run websites and APIs.
2. **Machine Learning** – Train models on GPU-powered instances.
3. **Big Data Processing** – Run Hadoop/Spark clusters.
4. **CI/CD Runners** – Automate builds and deployments.
5. **Gaming Servers** – Host multiplayer servers.
6. **Microservices** – Run backend services in scalable environments.

---

## 🧭 Example Workflow

1. Go to **AWS Management Console → EC2 → Launch Instance**
2. Choose an **AMI** (e.g., Ubuntu 22.04)
3. Select **Instance Type** (e.g., t3.micro)
4. Configure **Network, Security Groups, Storage**
5. Create or select a **Key Pair**
6. Launch and **Connect via SSH**
7. Deploy your application 🚀

---

## 🧾 Monitoring Tools

* **Amazon CloudWatch** → Monitor performance (CPU, memory, disk I/O)
* **AWS CloudTrail** → Logs who did what in AWS
* **EC2 Dashboard** → Overview of running instances and costs

---

## 🌐 Public vs Private EC2

### Public EC2 Instance

A **public EC2 instance** is an instance that has **direct access to the internet** and can be accessed from the internet.

**Characteristics:**
- ✅ Assigned a **public IP address** or **Elastic IP**
- ✅ Resides in a **public subnet**
- ✅ Route table includes route to **Internet Gateway** (`0.0.0.0/0 → igw-xxx`)
- ✅ Can receive inbound traffic from internet (based on Security Group rules)
- ✅ Can make outbound requests to internet

**Architecture:**
```
Internet
   ↕
Internet Gateway
   ↕
Public Subnet (10.0.1.0/24)
   ↕
EC2 Instance (Public IP: 54.123.45.67)
```

**Use Cases:**
- Web servers (Nginx, Apache)
- Application servers with public APIs
- Bastion hosts (jump boxes)
- NAT instances

**Security Considerations:**
- ⚠️ Exposed to internet - more attack surface
- ⚠️ Requires strict Security Group rules
- ⚠️ Consider using ALB/NLB instead of direct exposure
- ⚠️ Always use HTTPS, disable unused ports

**Example Configuration:**
```bash
# Launch public EC2 instance
aws ec2 run-instances \
  --image-id ami-0c55b159cbfafe1f0 \
  --instance-type t3.micro \
  --subnet-id subnet-public123 \
  --security-group-ids sg-web-server \
  --associate-public-ip-address \
  --key-name my-key-pair
```

---

### Private EC2 Instance

A **private EC2 instance** resides in a private subnet and **cannot be directly accessed from the internet**.

**Characteristics:**
- ❌ No public IP address
- ✅ Only has **private IP** (from VPC CIDR range)
- ✅ Resides in a **private subnet**
- ❌ No direct route to Internet Gateway
- ✅ Can access internet via **NAT Gateway/Instance** (outbound only)
- ❌ Cannot receive inbound traffic from internet

**Architecture:**
```
Internet
   ↕
Internet Gateway
   ↕
NAT Gateway (in public subnet)
   ↕
Private Subnet (10.0.2.0/24)
   ↕
EC2 Instance (Private IP: 10.0.2.50 only)
```

**Use Cases:**
- Application servers
- Database servers
- Backend microservices
- Processing workers
- Internal tools

**Accessing Private Instances:**

**Option 1: Bastion Host (Jump Box)**
```
Your Computer → SSH → Bastion (Public) → SSH → Private Instance
```

```bash
# SSH through bastion
ssh -i key.pem -J ec2-user@bastion-ip ec2-user@private-ip
```

**Option 2: VPN Connection**
```
Your Network → VPN → VPC → Private Instance
```

**Option 3: AWS Systems Manager (SSM) Session Manager**
```bash
# No SSH required, no bastion needed!
aws ssm start-session --target i-1234567890abcdef0
```

**Internet Access for Private Instances:**

Private instances can access internet through:

**NAT Gateway (Recommended):**
- Managed AWS service
- Highly available within AZ
- Scales automatically
- Located in public subnet

```bash
# Create NAT Gateway
aws ec2 create-nat-gateway \
  --subnet-id subnet-public123 \
  --allocation-id eipalloc-abc123

# Add route in private subnet route table
aws ec2 create-route \
  --route-table-id rtb-private \
  --destination-cidr-block 0.0.0.0/0 \
  --nat-gateway-id nat-xyz789
```

**NAT Instance (Legacy, not recommended):**
- EC2 instance you manage
- More configuration needed
- Single point of failure

**Comparison:**

| Feature | Public EC2 | Private EC2 |
|---------|-----------|-------------|
| **Public IP** | Yes | No |
| **Internet Access** | Direct (via IGW) | Via NAT (outbound only) |
| **Accessible from Internet** | Yes (with SG rules) | No |
| **Subnet Type** | Public | Private |
| **Security** | Lower (exposed) | Higher (isolated) |
| **Use Case** | Web servers, APIs | Databases, app servers |
| **Cost** | Lower (no NAT) | Higher (NAT Gateway charges) |

**Best Practice Architecture:**
```
┌─────────────────────────────────────────────────────┐
│                    VPC (10.0.0.0/16)                │
│                                                      │
│  ┌─────────────────────┐  ┌─────────────────────┐  │
│  │  Public Subnet      │  │  Private Subnet     │  │
│  │  10.0.1.0/24        │  │  10.0.2.0/24        │  │
│  │                     │  │                     │  │
│  │  ┌──────────────┐   │  │  ┌──────────────┐  │  │
│  │  │ ALB/NLB      │   │  │  │ App Server   │  │  │
│  │  │ (Public IP)  │───┼──┼─▶│ (Private IP) │  │  │
│  │  └──────────────┘   │  │  └──────────────┘  │  │
│  │                     │  │         │           │  │
│  │  ┌──────────────┐   │  │         ▼           │  │
│  │  │ NAT Gateway  │   │  │  ┌──────────────┐  │  │
│  │  └──────────────┘   │  │  │ RDS          │  │  │
│  │         │           │  │  │ (Private IP) │  │  │
│  └─────────┼───────────┘  │  └──────────────┘  │  │
│            │              │         ▲           │  │
│            │              └─────────┼───────────┘  │
│            │                        │              │
│      ┌─────▼──────┐           ┌────┴──────┐       │
│      │  Internet  │           │  Private  │       │
│      │  Gateway   │           │  Route    │       │
│      └────────────┘           │  Table    │       │
└─────────────────────────────────────────────────────┘
```

---

## 🧩 What is Amazon RDS?

**Amazon RDS (Relational Database Service)** is a **managed relational database service** by AWS that lets you **create, operate, and scale databases in the cloud** — without worrying about server management, software patching, backups, or scaling manually.

In simple terms:

> ✅ AWS RDS takes care of all the “boring” database administration tasks so you can focus on building applications.

---

## ⚙️ Supported Database Engines

Amazon RDS supports **multiple relational database engines**:

| Database Engine   | Description                                                                           |
| ----------------- | ------------------------------------------------------------------------------------- |
| **Amazon Aurora** | AWS’s own MySQL/PostgreSQL-compatible engine optimized for performance & scalability. |
| **MySQL**         | Most popular open-source database, widely supported.                                  |
| **PostgreSQL**    | Advanced open-source DB with rich features (JSON, GIS, etc.).                         |
| **MariaDB**       | Fork of MySQL with similar compatibility.                                             |
| **Oracle**        | Commercial database, license required.                                                |
| **SQL Server**    | Microsoft’s relational DB (Express, Web, Standard, Enterprise editions).              |

---

## 🧠 Why Use RDS Instead of Self-Hosting a Database?

With a self-managed database (on EC2, for example), you must handle:

* Installation
* Backups
* Patching
* Scaling
* Failover setup
* Performance tuning

With **RDS**, AWS handles most of this automatically:
✅ **Automated backups**
✅ **Automatic failover** (Multi-AZ)
✅ **Monitoring and alerts**
✅ **Security & patch management**
✅ **Vertical/Horizontal scaling**

---

## 🧩 Key Components and Concepts

### 1. **DB Instance**

A **DB instance** is the **primary RDS resource** — it represents a running database environment with compute and memory capacity.
You choose:

* Instance type (like EC2)
* Storage size
* Database engine
* Networking setup

Example:
`db.t3.micro` (small, cheap)
`db.m6g.large` (general purpose)
`db.r6g.2xlarge` (memory optimized)

---

### 2. **DB Subnet Group**

A **DB Subnet Group** defines which subnets (in your VPC) your RDS instance can use.
Used to ensure redundancy across **multiple Availability Zones (AZs)**.

---

### 3. **Multi-AZ Deployment**

For **high availability**:

* Primary DB in one Availability Zone
* Standby replica automatically maintained in another AZ
* AWS automatically **fails over** to standby in case of failure

✅ No manual intervention
✅ No data loss (synchronous replication)
✅ Automatic failover

---

### 4. **Read Replicas**

For **read scalability** and **disaster recovery**:

* RDS can create **read-only replicas** of your primary database.
* Applications can route read queries to replicas.
* If needed, you can **promote a replica** to a standalone database.

Works with: MySQL, PostgreSQL, MariaDB, Aurora.

---

### 5. **Storage Types**

RDS offers multiple storage types depending on performance needs:

| Storage Type                   | Description                   | Use Case                |
| ------------------------------ | ----------------------------- | ----------------------- |
| **General Purpose (gp3/gp2)**  | Balanced performance and cost | Most workloads          |
| **Provisioned IOPS (io1/io2)** | High-performance, low-latency | Critical production DBs |
| **Magnetic (deprecated)**      | Older, slower                 | Legacy systems          |

Storage automatically **resizes** in Aurora and can be **manually increased** in other engines.

---

### 6. **Automated Backups & Snapshots**

* **Automated Backups:** Daily backups + transaction logs; allows **point-in-time recovery** (e.g., restore to 2:35 PM yesterday).
* **Manual Snapshots:** User-initiated, stored indefinitely until deleted.

Backups are stored in **Amazon S3**.

---

### 7. **Monitoring and Logging**

* **Amazon CloudWatch:** Monitors CPU, memory, IOPS, connections, etc.
* **Enhanced Monitoring:** OS-level metrics in real time.
* **Performance Insights:** Advanced performance analytics (query bottlenecks, load visualization).

---

### 8. **Security**

Security is a major RDS feature:

| Security Feature          | Description                                            |
| ------------------------- | ------------------------------------------------------ |
| **IAM Authentication**    | Use AWS IAM credentials instead of DB passwords.       |
| **Encryption at Rest**    | Encrypts data using AWS KMS.                           |
| **Encryption in Transit** | Uses SSL/TLS between clients and RDS.                  |
| **VPC Isolation**         | Run RDS in your private network.                       |
| **Security Groups**       | Control inbound/outbound access at the instance level. |

---

### 9. **Parameter Groups**

A **parameter group** acts like a config file for your database (e.g., `my.cnf`, `postgresql.conf`).
You can customize settings such as:

* `max_connections`
* `query_cache_size`
* `log_statement`

---

### 10. **Maintenance**

AWS automatically:

* Patches your database engine
* Updates hardware/software
* Performs maintenance during your **preferred window**

You can defer or control when maintenance happens.

---

## 🧮 RDS Instance Classes

Just like EC2, RDS uses **instance classes** to define CPU, RAM, and network.

| Instance Family     | Description                      |
| ------------------- | -------------------------------- |
| **db.t3 / db.t4g**  | Burstable, low-cost for dev/test |
| **db.m6g / db.m7g** | General purpose                  |
| **db.r6g / db.r7g** | Memory-optimized                 |
| **db.x2g / db.z1d** | High-performance workloads       |

All can be deployed in **Single-AZ** or **Multi-AZ**.

---

## 🧰 Scaling Options

| Type                     | Description                                                |
| ------------------------ | ---------------------------------------------------------- |
| **Vertical Scaling**     | Change instance size (e.g., `db.t3.small` → `db.m5.large`) |
| **Horizontal Scaling**   | Add Read Replicas for read-heavy workloads                 |
| **Storage Auto-Scaling** | Automatically expands volume size as needed                |

Aurora can **auto-scale compute and storage** seamlessly.

---

## 🧾 Pricing Models

| Model                   | Description                                                |
| ----------------------- | ---------------------------------------------------------- |
| **On-Demand**           | Pay per hour — good for unpredictable workloads            |
| **Reserved Instance**   | 1–3 year commitment for up to 60% discount                 |
| **Serverless (Aurora)** | Pay per second of usage — ideal for intermittent workloads |

You also pay for:

* Storage (per GB/month)
* I/O requests
* Backup storage
* Data transfer

---

## 🧑‍💻 Common Use Cases

1. **Web Application Backends** (e.g., Laravel, Django, Express.js)
2. **Analytics Dashboards**
3. **ERP/CRM Systems**
4. **Mobile App APIs**
5. **E-commerce Databases**
6. **Data Warehousing (Aurora + Redshift combo)**

---

## 🧭 Example Workflow (Creating RDS Instance)

1. Go to **AWS Console → RDS → Create database**
2. Choose **Engine** (MySQL, PostgreSQL, etc.)
3. Select **Use case** (Production / Dev/Test)
4. Configure:

   * DB instance size (e.g., `db.t3.micro`)
   * Storage (e.g., 20GB gp3)
   * Master username/password
   * VPC and Subnet
5. Configure **Backup, Encryption, Monitoring**
6. Launch the instance 🚀
7. Connect using endpoint:

   ```
   mysql -h your-db-endpoint.rds.amazonaws.com -u admin -p
   ```

---

## 🧩 Amazon Aurora (Special Case)

**Amazon Aurora** is a **cloud-native relational database engine** built by AWS that is:

* Compatible with **MySQL** and **PostgreSQL**
* Up to **5× faster than MySQL** and **3× faster than PostgreSQL**
* Automatically **scales storage up to 128TB**
* Can **auto-heal**, **auto-scale**, and supports **serverless mode**

Aurora is often considered the **“next-gen RDS”** for production-grade workloads.

---

## 🧱 Integrations

* **AWS Lambda** → trigger DB events or serverless processing
* **AWS CloudWatch** → performance monitoring
* **AWS Secrets Manager** → securely store DB credentials
* **AWS DMS (Database Migration Service)** → migrate existing DBs to RDS
* **Amazon S3** → store backups, import/export data

---

## 🧯 Disaster Recovery

* **Multi-AZ** ensures automatic failover
* **Cross-Region Read Replicas** for geographic redundancy
* **Backups & Snapshots** for point-in-time restore

---

## 🔒 Private RDS

### What is a Private RDS Instance?

A **private RDS instance** is a database that resides in a **private subnet** within your VPC and **cannot be accessed directly from the internet**.

### Why Use Private RDS?

✅ **Security Best Practice:**
- Databases should NEVER be directly exposed to the internet
- Reduces attack surface
- Protects sensitive data
- Complies with security standards (PCI-DSS, HIPAA, etc.)

### Architecture

```
┌──────────────────────────────────────────────────────┐
│                  VPC (10.0.0.0/16)                   │
│                                                       │
│  ┌────────────────────┐   ┌─────────────────────┐   │
│  │  Public Subnet     │   │  Private Subnet     │   │
│  │  10.0.1.0/24       │   │  10.0.2.0/24        │   │
│  │                    │   │                     │   │
│  │  ┌──────────────┐  │   │  ┌──────────────┐  │   │
│  │  │  Web Server  │  │   │  │  App Server  │  │   │
│  │  │  (Public IP) │──┼───┼─▶│ (Private IP) │  │   │
│  │  └──────────────┘  │   │  └──────────────┘  │   │
│  │                    │   │         │           │   │
│  └────────────────────┘   │         ▼           │   │
│                           │  ┌──────────────┐  │   │
│                           │  │     RDS      │  │   │
│                           │  │  (Private)   │  │   │
│                           │  │  10.0.2.50   │  │   │
│                           │  └──────────────┘  │   │
│                           └─────────────────────┘   │
└──────────────────────────────────────────────────────┘
```

### Characteristics of Private RDS

| Characteristic | Details |
|---------------|---------|
| **Public Access** | Disabled (no public IP) |
| **Subnet** | Private subnet only |
| **Access** | Only from within VPC or via VPN/Direct Connect |
| **Security Group** | Restricts connections to specific sources (e.g., app servers) |
| **Endpoint** | Private DNS endpoint within VPC |

### How to Access Private RDS

**Option 1: From EC2 in Same VPC (Most Common)**
```bash
# From app server EC2 instance
mysql -h mydb.abc123.us-east-1.rds.amazonaws.com -u admin -p
```

**Option 2: Bastion Host (Jump Box)**
```
Local Machine → SSH → Bastion (Public) → RDS (Private)
```

```bash
# SSH tunnel through bastion
ssh -i key.pem -L 3306:rds-endpoint:3306 ec2-user@bastion-ip

# Connect to localhost:3306
mysql -h 127.0.0.1 -P 3306 -u admin -p
```

**Option 3: VPN Connection**
```
Corporate Network → VPN → AWS VPC → RDS
```

**Option 4: AWS Direct Connect**
```
On-Premises Data Center → Dedicated Network → AWS → RDS
```

**Option 5: AWS Systems Manager Session Manager**
```bash
# Start session to EC2 instance in same VPC
aws ssm start-session --target i-1234567890abcdef0

# Then connect to RDS from that instance
mysql -h rds-endpoint -u admin -p
```

### Creating Private RDS Instance

**Key Settings:**

```bash
# AWS CLI Example
aws rds create-db-instance \
  --db-instance-identifier mydb \
  --db-instance-class db.t3.micro \
  --engine mysql \
  --master-username admin \
  --master-user-password mypassword \
  --allocated-storage 20 \
  --vpc-security-group-ids sg-private-rds \
  --db-subnet-group-name my-db-subnet-group \
  --no-publicly-accessible
```

**Critical Parameters:**
- `--no-publicly-accessible` → Ensures no public IP
- `--db-subnet-group-name` → Must use private subnets
- `--vpc-security-group-ids` → Restrict access

### DB Subnet Group for Private RDS

A **DB Subnet Group** defines which subnets RDS can use:

```bash
# Create DB Subnet Group with private subnets
aws rds create-db-subnet-group \
  --db-subnet-group-name my-private-subnets \
  --db-subnet-group-description "Private subnets for RDS" \
  --subnet-ids subnet-private1 subnet-private2
```

**Requirements:**
- Minimum 2 subnets in different Availability Zones
- All subnets must be private (no route to IGW)
- Subnets must be in the same VPC

### Security Group Configuration

**Database Security Group:**
```
Inbound Rules:
Type       Protocol  Port   Source              Description
MySQL      TCP       3306   sg-app-servers      Allow from app tier only
PostgreSQL TCP       5432   sg-backend          Allow from backend only

Outbound Rules:
Type  Protocol  Port  Destination  Description
All   All       All   0.0.0.0/0    Allow all outbound
```

### Multi-AZ Private RDS

For **high availability**, deploy private RDS in Multi-AZ:

```
┌─────────────────────────────────────────────┐
│               VPC (10.0.0.0/16)             │
│                                              │
│  ┌──────────────────┐  ┌──────────────────┐ │
│  │  Private Subnet  │  │  Private Subnet  │ │
│  │  AZ-1            │  │  AZ-2            │ │
│  │  10.0.2.0/24     │  │  10.0.3.0/24     │ │
│  │                  │  │                  │ │
│  │  ┌────────────┐  │  │  ┌────────────┐  │ │
│  │  │    RDS     │  │  │  │    RDS     │  │ │
│  │  │  Primary   │◀─┼──┼─▶│  Standby   │  │ │
│  │  │            │  │  │  │ (Replica)  │  │ │
│  │  └────────────┘  │  │  └────────────┘  │ │
│  └──────────────────┘  └──────────────────┘ │
│                                              │
│  Automatic Failover: ~60-120 seconds        │
└─────────────────────────────────────────────┘
```

### Read Replicas in Private Subnets

**Use Case:** Scale read traffic

```
Primary RDS (Private) → Read Replica 1 (Private)
                    → Read Replica 2 (Private)
```

**Setup:**
```bash
aws rds create-db-instance-read-replica \
  --db-instance-identifier mydb-replica \
  --source-db-instance-identifier mydb \
  --db-instance-class db.t3.micro \
  --no-publicly-accessible
```

### Common Pitfalls and Solutions

**Problem:** Can't connect to private RDS from laptop
**Solution:** Use bastion host or VPN - this is by design!

**Problem:** Application can't reach RDS
**Solution:** Check:
- ✅ App and RDS in same VPC
- ✅ Security group allows traffic from app's security group
- ✅ Subnet route table properly configured
- ✅ Network ACLs not blocking traffic

**Problem:** Want to access for debugging
**Solution:**
- Use AWS Systems Manager Session Manager (no bastion needed)
- Temporarily use bastion host
- Set up VPN for permanent access

### Best Practices

✅ **Do:**
- Always deploy RDS in private subnets for production
- Use security groups to whitelist only necessary sources
- Enable Multi-AZ for production databases
- Use IAM database authentication when possible
- Enable encryption at rest and in transit
- Regularly review security group rules

❌ **Don't:**
- Enable public access on production databases
- Allow `0.0.0.0/0` in security group inbound rules
- Put RDS in public subnet
- Use single AZ for critical databases

### Private RDS vs Public RDS

| Feature | Private RDS | Public RDS |
|---------|-------------|------------|
| **Public IP** | ❌ No | ✅ Yes |
| **Accessible from Internet** | ❌ No | ✅ Yes (if SG allows) |
| **Security** | ✅ Higher | ⚠️ Lower (exposed) |
| **Use Case** | ✅ Production | ❌ Testing only |
| **VPN Required** | Sometimes | No |
| **Compliance** | ✅ Meets standards | ❌ Often violates |
| **Cost** | Same | Same |

**💡 Recommendation:** Always use **Private RDS** for production. Only use public RDS for temporary testing or development.

---

## 🧩 Summary

| Feature                 | Description                                                |
| ----------------------- | ---------------------------------------------------------- |
| **Service Type**        | Managed relational database                                |
| **Databases Supported** | MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora     |
| **Scaling**             | Vertical, Horizontal (Read Replicas), Storage Auto-scaling |
| **Availability**        | Multi-AZ deployments                                       |
| **Security**            | IAM, VPC, Encryption, Security Groups                      |
| **Automation**          | Backups, patching, maintenance                             |
| **Monitoring**          | CloudWatch, Performance Insights                           |
| **Pricing**             | On-demand, Reserved, Serverless (Aurora)                   |

---

## 🧩 What is Amazon S3?

**Amazon Simple Storage Service (S3)** is an **object storage service** that lets you store and retrieve **any amount of data, at any time, from anywhere on the web**.

In simple words:

> 🗄️ It’s a massive, scalable storage system for files, images, videos, backups, logs, static websites, and more.

S3 is **not** like a traditional filesystem or database — it’s designed to store **objects**, not files or blocks.

---

## 🧠 Key Characteristics

| Feature             | Description                                               |
| ------------------- | --------------------------------------------------------- |
| **Type of Storage** | Object storage (data stored as objects)                   |
| **Access**          | HTTP/HTTPS via REST API, AWS CLI, or SDK                  |
| **Durability**      | 99.999999999% (11 nines) — almost impossible to lose data |
| **Availability**    | 99.99% uptime SLA                                         |
| **Scalability**     | Virtually unlimited storage                               |
| **Cost**            | Pay only for what you use (GBs, requests, data transfer)  |

---

## 🧱 Core Concepts

### 1. **Buckets**

A **bucket** is like a **top-level folder** where you store your objects.

* Each bucket must have a **globally unique name**.
* You can have multiple buckets per account.
* You can set **region**, **permissions**, and **policies** per bucket.

Example:

```
my-company-data-bucket
my-website-assets
logs-archive-2025
```

---

### 2. **Objects**

An **object** is the **actual data** stored in S3.
Each object consists of:

* **Key** (the file name/path)
* **Value** (the actual data)
* **Metadata** (optional custom or system-defined data)
* **Version ID** (if versioning is enabled)
* **ACLs/permissions**

Example structure:

```
Bucket: my-website-assets
Key: images/logo.png
Value: binary data of image
Metadata: Content-Type = image/png
```

---

### 3. **Keys**

A **key** is the **unique identifier** for an object in a bucket — like a full file path.

Example:

```
/user/photos/profile.jpg
/documents/reports/2025.pdf
```

---

### 4. **Regions**

Buckets are created in a specific **AWS region** (e.g., `us-east-1`, `ap-south-1`).
You should choose a region **close to your users** to reduce latency and cost.

---

### 5. **Data Consistency**

* **Read-after-write consistency** for new objects.
* **Strong consistency** for overwrite and delete operations (since Dec 2020).

---

## 🔐 Security and Access Control

Amazon S3 provides **multiple layers of security**:

### 1. **IAM Policies**

Control who (user, role, service) can access buckets and objects.

Example: Allow only specific users to upload files.

---

### 2. **Bucket Policies**

Define permissions directly on a bucket (in JSON).
Can make buckets:

* **Private** (default)
* **Public** (for hosting static websites)
* **Restricted by IP or VPC**

Example (Public read access):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::my-website-assets/*"]
    }
  ]
}
```

---

### 3. **Access Control Lists (ACLs)**

Older method of controlling access at object or bucket level (less recommended now).

---

### 4. **Encryption**

* **Server-Side Encryption (SSE):**

  * SSE-S3 → Managed by S3 (default)
  * SSE-KMS → Managed by AWS Key Management Service
  * SSE-C → Customer-provided key
* **Client-Side Encryption:** Encrypt data before uploading.

---

### 5. **Block Public Access**

A strong safety feature that **prevents accidental public exposure**.
Highly recommended to keep it enabled unless you intentionally host public data.

---

## 💾 Storage Classes

S3 offers **different storage classes** based on access frequency and cost:

| Storage Class                          | Description                                  | Durability | Availability | Use Case                         |
| -------------------------------------- | -------------------------------------------- | ---------- | ------------ | -------------------------------- |
| **S3 Standard**                        | Frequent access                              | 11 nines   | 99.99%       | General purpose                  |
| **S3 Intelligent-Tiering**             | Auto-moves data between tiers based on usage | 11 nines   | 99.9–99.99%  | Unknown access patterns          |
| **S3 Standard-IA (Infrequent Access)** | Lower cost, infrequent access                | 11 nines   | 99.9%        | Backup, DR                       |
| **S3 One Zone-IA**                     | Stored in one AZ only                        | 11 nines   | 99.5%        | Non-critical, secondary backups  |
| **S3 Glacier Instant Retrieval**       | Low-cost archive, instant retrieval          | 11 nines   | 99.9%        | Archival with fast access        |
| **S3 Glacier Flexible Retrieval**      | Cheapest archive, minutes to hours retrieval | 11 nines   | 99.99%       | Long-term archives               |
| **S3 Glacier Deep Archive**            | Lowest cost, 12-hour retrieval               | 11 nines   | 99.99%       | Compliance, rarely accessed data |

---

## 🔁 Lifecycle Management

You can **automatically move or delete data** using lifecycle rules.

Examples:

* Move files older than 30 days to Glacier.
* Delete logs after 1 year.
* Transition data to Intelligent-Tiering.

Example rule:

```
After 30 days → Move to Standard-IA
After 180 days → Move to Glacier
After 365 days → Delete
```

---

## 🧰 Versioning

* Keep **multiple versions** of the same object.
* Protects against accidental overwrites or deletions.
* Can be combined with **MFA Delete** for extra safety.

Example:

```
file.txt (Version 1)
file.txt (Version 2)
file.txt (Version 3)
```

---

## 🧯 Replication

### 1. **Cross-Region Replication (CRR)**

Replicates data automatically to a bucket in another region for **disaster recovery** or **geo-redundancy**.

### 2. **Same-Region Replication (SRR)**

Replicates within the same region — useful for **compliance** or **separation of workloads**.

---

## 🌍 Static Website Hosting

S3 can host **static websites** (HTML, CSS, JS, images).

Steps:

1. Upload your files.
2. Enable **Static Website Hosting**.
3. Set **index.html** and **error.html**.
4. Make the bucket public or use **CloudFront** for CDN.

Example website endpoint:

```
http://my-website-assets.s3-website-us-east-1.amazonaws.com
```

---

## 🧮 Cost Structure

You pay for:

1. **Storage** (per GB/month)
2. **Requests** (PUT, GET, DELETE)
3. **Data transfer** (outbound to internet)
4. **Lifecycle transitions**
5. **Replication and analytics**

💡 **Tip:** Use **S3 Cost Explorer** or **AWS Budgets** to monitor and optimize costs.

---

## 📊 Monitoring & Logging

* **S3 Access Logs:** Track all access requests to your bucket.
* **AWS CloudTrail:** Record API-level activity for security auditing.
* **Amazon CloudWatch:** Monitor storage metrics (size, request count, errors).

---

## 🧠 Advanced Features

| Feature                    | Description                                           |
| -------------------------- | ----------------------------------------------------- |
| **S3 Object Lock**         | Prevents data deletion for compliance (WORM)          |
| **S3 Select**              | Query partial data from an object using SQL           |
| **S3 Inventory**           | Generates reports of objects and metadata             |
| **S3 Batch Operations**    | Perform bulk actions (like copying or tagging)        |
| **S3 Access Points**       | Simplify managing access for shared buckets           |
| **S3 Event Notifications** | Trigger AWS Lambda or SNS/SQS when files are uploaded |

---

## 🧩 Common Use Cases

1. 🗃️ **Backup and restore** — database dumps, server snapshots
2. 🖼️ **Media hosting** — images, videos, documents
3. 🧾 **Log storage** — application logs, analytics pipelines
4. 💻 **Static website hosting** — serverless websites
5. 🧠 **Machine Learning datasets** — store large datasets for training models
6. 🧰 **Software distribution** — app installers, updates
7. 🧾 **Data lake** — central repository for raw and processed data

---

## 🧭 Example Workflow (Uploading and Accessing Files)

1. Create a bucket:

   ```bash
   aws s3 mb s3://my-data-bucket --region ap-south-1
   ```
2. Upload a file:

   ```bash
   aws s3 cp report.pdf s3://my-data-bucket/reports/
   ```
3. Make it public (optional):

   ```bash
   aws s3api put-object-acl --bucket my-data-bucket --key reports/report.pdf --acl public-read
   ```
4. Access it:

   ```
   https://my-data-bucket.s3.ap-south-1.amazonaws.com/reports/report.pdf
   ```

---

## 🧾 Summary Table

| Feature              | Description                                        |
| -------------------- | -------------------------------------------------- |
| **Type**             | Object Storage                                     |
| **Durability**       | 99.999999999%                                      |
| **Availability**     | 99.99%                                             |
| **Data Model**       | Buckets and Objects                                |
| **Access Methods**   | API, CLI, SDK, Console                             |
| **Security**         | IAM, Bucket Policy, Encryption                     |
| **Scalability**      | Virtually unlimited                                |
| **Common Use Cases** | Backup, hosting, ML data, archiving                |
| **Pricing**          | Pay-as-you-go (storage + requests + data transfer) |

---

## 🧩 What is a Security Group?

A **Security Group (SG)** is a **virtual firewall** attached to your **EC2 instances**, **RDS databases**, or **other AWS resources** to control network traffic.

You can think of it as:

> 🧱 A set of rules that decide which traffic is allowed **in or out** of your resource.

Security Groups operate at the **instance level** within a **VPC (Virtual Private Cloud)**.

---

## ⚙️ Key Characteristics

| Feature              | Description                                              |
| -------------------- | -------------------------------------------------------- |
| **Type**             | Virtual firewall                                         |
| **Scope**            | Instance-level (not subnet-level)                        |
| **Direction**        | Inbound and Outbound rules                               |
| **Stateful**         | Return traffic is automatically allowed                  |
| **Default Behavior** | All inbound traffic denied, all outbound traffic allowed |
| **Attachments**      | Can attach multiple SGs to a single instance             |

---

## 🧠 How Security Groups Work

When you launch an EC2 instance or RDS database, you **assign** one or more security groups to it.

Each rule in the group allows specific **protocols**, **ports**, and **sources/destinations**.

For example:

* Allow SSH (TCP port 22) from your IP.
* Allow HTTP (TCP port 80) from anywhere.
* Allow MySQL (TCP port 3306) only from a specific EC2 instance.

---

## 🧩 Security Group Rules

Security Groups have two types of rules:

### 1. **Inbound Rules (Ingress)**

Define what **incoming traffic** is allowed **to** your instance.

Example:

| Type  | Protocol | Port Range | Source         | Description              |
| ----- | -------- | ---------- | -------------- | ------------------------ |
| SSH   | TCP      | 22         | 203.0.113.0/24 | Allow SSH from office    |
| HTTP  | TCP      | 80         | 0.0.0.0/0      | Allow web traffic        |
| HTTPS | TCP      | 443        | 0.0.0.0/0      | Allow secure web traffic |

---

### 2. **Outbound Rules (Egress)**

Define what **outgoing traffic** is allowed **from** your instance.

Example:

| Type        | Protocol | Port Range | Destination | Description                          |
| ----------- | -------- | ---------- | ----------- | ------------------------------------ |
| All traffic | All      | All        | 0.0.0.0/0   | Allow all outbound traffic (default) |
| MySQL       | TCP      | 3306       | 10.0.1.0/24 | Allow DB access to private subnet    |

---

## 🧮 Example Scenario

Suppose you have:

* A web server on EC2 (public)
* A database on RDS (private)

You can define:

### Web Server Security Group (SG-Web)

* Inbound:

  * Allow HTTP (80) from anywhere
  * Allow HTTPS (443) from anywhere
  * Allow SSH (22) from your IP only
* Outbound:

  * Allow MySQL (3306) to SG-DB

### Database Security Group (SG-DB)

* Inbound:

  * Allow MySQL (3306) **from SG-Web**
* Outbound:

  * Allow all (default)

👉 This ensures **only your web server** can talk to your database — not the internet.

---

## 🧱 Stateful vs Stateless

Security Groups are **stateful**, meaning:

* If you allow inbound traffic, the **response** is automatically allowed.
* You don’t need to define separate outbound rules for the return path.

By contrast, **Network ACLs (NACLs)** are **stateless** (you must define both inbound and outbound).

---

## 🔐 Default Security Group

Every **VPC** automatically comes with a **default security group**.

Default behavior:

* **Inbound:** Allows traffic from instances assigned to the same SG.
* **Outbound:** Allows all traffic.
* Cannot delete it, but you can modify rules.

---

## 🧰 Rule Components

Each rule in a Security Group defines:

| Component              | Description                                   |
| ---------------------- | --------------------------------------------- |
| **Type**               | Common protocols (SSH, HTTP, HTTPS, etc.)     |
| **Protocol**           | TCP, UDP, ICMP, or custom                     |
| **Port Range**         | Specific port or range (e.g., 80, 1024–65535) |
| **Source/Destination** | IP range (CIDR), another SG, or prefix list   |
| **Description**        | Optional comment for clarity                  |

---

## 🌍 Sources and Destinations

| Type                  | Description                                                     |
| --------------------- | --------------------------------------------------------------- |
| **CIDR IP Range**     | Example: `0.0.0.0/0` (anywhere), `203.0.113.5/32` (specific IP) |
| **Security Group ID** | Allows traffic only from resources with that SG                 |
| **Prefix List**       | Predefined AWS service ranges (like S3 or CloudFront)           |

---

## 🧭 Practical Examples

### Example 1: Allow SSH from Specific IP

```bash
Type: SSH
Protocol: TCP
Port Range: 22
Source: 203.0.113.15/32
```

### Example 2: Allow HTTP/HTTPS from Anywhere

```bash
Type: HTTP
Protocol: TCP
Port Range: 80
Source: 0.0.0.0/0
Type: HTTPS
Protocol: TCP
Port Range: 443
Source: 0.0.0.0/0
```

### Example 3: Allow RDS Access from EC2

```bash
Type: MySQL/Aurora
Protocol: TCP
Port Range: 3306
Source: sg-0a1b2c3d4e5f6g7h (EC2 security group)
```

---

## 🧩 Common Mistakes to Avoid

1. **Allowing SSH (22) from 0.0.0.0/0**

   * This opens your instance to the whole internet.
   * Restrict SSH to your IP or use a bastion host.

2. **Forgetting to add outbound rules**

   * By default, outbound traffic is allowed — but if removed, your instance may lose internet access.

3. **Not using SG references**

   * Instead of IPs, use SG IDs for dynamic security between AWS resources.

4. **Overlapping Rules**

   * Avoid duplicate or conflicting rules; AWS evaluates all allow rules together.

---

## 🧠 Relationship with Other AWS Services

| Service                         | How Security Groups Work                           |
| ------------------------------- | -------------------------------------------------- |
| **EC2**                         | Controls instance traffic                          |
| **RDS**                         | Controls DB access (e.g., from web server SG)      |
| **Elastic Load Balancer (ELB)** | Controls inbound traffic from users to backend EC2 |
| **Lambda (in VPC)**             | Controls outbound access if attached to subnets    |
| **ECS / EKS**                   | Controls traffic to containers or pods in VPC      |

---

## 🧯 Logging and Monitoring

Security Groups themselves don’t log traffic, but you can monitor them with:

* **VPC Flow Logs** → captures allowed and denied traffic
* **AWS Config** → tracks SG configuration changes
* **CloudTrail** → records API calls that modify SGs

---

## 🧮 Scaling and Limits

| Item                      | Default Limit           |
| ------------------------- | ----------------------- |
| Security groups per VPC   | 2,500                   |
| Rules per security group  | 60 inbound, 60 outbound |
| SGs per network interface | 5                       |
| Rules evaluated per ENI   | 300 total               |

(These limits can be increased via AWS Support.)

---

## 🧰 Best Practices

1. **Use least privilege:** Allow only required ports and IPs.
2. **Use SG references:** Instead of fixed IPs, use other SGs for flexibility.
3. **Regularly audit SGs:** Remove unused rules.
4. **Use descriptive names:** Example — `sg-webserver`, `sg-database`.
5. **Combine with NACLs:** Layered security at subnet + instance level.
6. **Restrict SSH/RDP:** Use bastion hosts or AWS Systems Manager Session Manager.
7. **Enable VPC Flow Logs:** For visibility and debugging.

---

## 🧩 Example: Web Application Architecture

Here’s a common setup:

| Layer                    | Instance Type  | Security Group Rules                                   |
| ------------------------ | -------------- | ------------------------------------------------------ |
| **Public (Web Tier)**    | EC2 Web Server | Inbound: 80/443 from Internet, Outbound: 3306 to SG-DB |
| **Private (DB Tier)**    | RDS MySQL      | Inbound: 3306 from SG-Web, Outbound: All               |
| **Bastion Host (Admin)** | EC2 Jumpbox    | Inbound: 22 from Admin IP, Outbound: All               |

This ensures **controlled, layered access** — the internet never directly reaches the database.

---

## 🧾 Summary

| Feature           | Description                                |
| ----------------- | ------------------------------------------ |
| **Type**          | Virtual firewall for instances             |
| **Scope**         | Instance-level (within VPC)                |
| **Stateful**      | Yes (return traffic automatically allowed) |
| **Default Rules** | Inbound: deny all, Outbound: allow all     |
| **Direction**     | Inbound and Outbound rules                 |
| **Association**   | EC2, RDS, Load Balancer, etc.              |
| **Best Practice** | Use least privilege and SG references      |

---
## 📚 References

- EC2: https://docs.aws.amazon.com/ec2/
- RDS: https://docs.aws.amazon.com/rds/
- S3: https://docs.aws.amazon.com/s3/
- Security Groups: https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html
- EBS: https://docs.aws.amazon.com/ebs/
- EFS: https://docs.aws.amazon.com/efs/

## 🧩 What is Amazon EBS?

**Amazon Elastic Block Store (EBS)** is a **block-level storage service** designed to be used with **EC2 instances**.
It provides **persistent, high-performance storage volumes** that behave like **virtual hard drives**.

When you launch an EC2 instance, you can **attach an EBS volume** to store your operating system, applications, and data.

---

## ⚙️ Key Characteristics of EBS

| Feature                   | Description                                                                      |
| ------------------------- | -------------------------------------------------------------------------------- |
| **Block storage**         | Data stored in fixed-size blocks (like a hard disk), unlike object storage (S3). |
| **Persistent**            | Data persists even after stopping or restarting the EC2 instance.                |
| **Attachable**            | You can attach/detach volumes from EC2 instances easily.                         |
| **Scalable**              | Can increase volume size, change type, and adjust performance without downtime.  |
| **Available and durable** | Replicated automatically within the same Availability Zone (AZ).                 |
| **Encrypted**             | Supports encryption at rest and in transit using AWS KMS.                        |

---

## 🧱 EBS vs Other Storage Types

| Feature     | EBS                 | S3                      | Instance Store                |
| ----------- | ------------------- | ----------------------- | ----------------------------- |
| Type        | Block storage       | Object storage          | Ephemeral block storage       |
| Use case    | Databases, OS disks | Backup, media, archives | Temporary cache, scratch data |
| Persistence | Persistent          | Persistent              | Lost when instance stops      |
| Access      | Mounted to one EC2  | HTTP API                | Only within instance          |
| Latency     | Low                 | Medium                  | Very low                      |

---

## 🧰 Types of EBS Volumes

EBS provides different **volume types** optimized for different workloads.

### 🧮 1. SSD-based (for transactional workloads)

These are designed for **high IOPS (Input/Output operations per second)**.

| Type                                 | Use Case              | Description                                                               |
| ------------------------------------ | --------------------- | ------------------------------------------------------------------------- |
| **gp3 (General Purpose SSD)**        | Most common           | Balances price and performance (baseline 3,000 IOPS, scalable to 16,000). |
| **io1 / io2 (Provisioned IOPS SSD)** | Databases             | High-performance volumes with provisioned IOPS (up to 64,000 IOPS).       |
| **io2 Block Express**                | Mission-critical apps | Ultra-low latency, designed for enterprise workloads.                     |

---

### 💽 2. HDD-based (for throughput workloads)

Designed for **large, sequential I/O operations** (like log processing or big data).

| Type                               | Use Case                   | Description                                                    |
| ---------------------------------- | -------------------------- | -------------------------------------------------------------- |
| **st1 (Throughput Optimized HDD)** | Big data, data warehousing | Cheaper storage optimized for large sequential reads/writes.   |
| **sc1 (Cold HDD)**                 | Infrequently accessed data | Lowest-cost HDD option for less frequently accessed workloads. |

---

## 🧠 How EBS Works

### 1. **Creating and Attaching**

* You create an **EBS volume** in a specific **Availability Zone (AZ)**.
* You attach it to an **EC2 instance** running in the same AZ.
* The OS sees it as a **block device** (e.g., `/dev/xvdf` on Linux).

### 2. **Mounting**

Once attached, you:

* Format it (e.g., `ext4`, `xfs`, `NTFS`)
* Mount it to a directory
* Use it just like a regular hard drive

Example:

```bash
sudo mkfs -t ext4 /dev/xvdf
sudo mkdir /data
sudo mount /dev/xvdf /data
```

### 3. **Detaching**

You can unmount and detach an EBS volume safely, then reattach it to another instance.

---

## 🧾 Important Concepts

### 🔹 Volume

The actual **virtual disk** you attach to your EC2 instance.

### 🔹 Snapshot

A **point-in-time backup** of an EBS volume stored in **Amazon S3**.

* Incremental (only changes are saved)
* Used for backup, disaster recovery, or creating new volumes.

Example:

```bash
aws ec2 create-snapshot --volume-id vol-1234567890abcdef --description "Backup"
```

### 🔹 Encryption

EBS provides **encryption at rest** using **AWS KMS**:

* Automatically encrypts data, snapshots, and copies.
* No need for application-level encryption.

You can also **encrypt existing unencrypted volumes** by:

1. Creating a snapshot
2. Copying it with encryption enabled
3. Creating a new encrypted volume from the snapshot

---

## 📊 EBS Performance Metrics

| Metric                                        | Description                                |
| --------------------------------------------- | ------------------------------------------ |
| **IOPS (Input/Output Operations per Second)** | Number of read/write operations per second |
| **Throughput**                                | Data transfer rate (MB/s)                  |
| **Latency**                                   | Time to complete a single I/O operation    |
| **Queue Length**                              | Number of pending I/O requests             |

You can monitor these metrics using **Amazon CloudWatch**.

---

## 🧩 Advanced EBS Features

### 1. **EBS Multi-Attach**

* Attach a single `io1/io2` volume to **multiple EC2 instances** in the same AZ.
* Used for cluster-aware applications (e.g., databases that support shared storage).

---

### 2. **Elastic Volumes**

* Modify volume **size**, **type**, or **performance (IOPS)** dynamically **without downtime**.

Example use case:

> You started with 100 GB gp3, and now your database needs 200 GB — just modify it on the fly!

---

### 3. **Fast Snapshot Restore (FSR)**

* Makes new EBS volumes created from snapshots **instantly ready** (no initialization time).

---

### 4. **Lifecycle Manager**

* Automates **snapshot creation, retention, and deletion** based on policies.

Example:

> Automatically back up every 6 hours and retain for 7 days.

---

### 5. **Cross-Region / Cross-Account Snapshots**

* Copy snapshots to other AWS regions for **disaster recovery** or to **share with other accounts**.

---

## 🔒 Security

1. **Encryption at rest** using AWS KMS.
2. **Encryption in transit** between EC2 and EBS.
3. **Access control** via IAM policies.
4. **Snapshots** inherit encryption settings.
5. **AWS CloudTrail** logs all snapshot and volume activities for auditing.

---

## 💰 Pricing Overview

You pay for:

* **Volume storage (per GB per month)**
* **Provisioned IOPS (if applicable)**
* **Snapshots stored in S3**
* **Data transfer** (for cross-region copies)

Example (approximate):

| Volume Type | Cost (USD/GB-month) | Notes                    |
| ----------- | ------------------- | ------------------------ |
| gp3         | ~$0.08              | General-purpose SSD      |
| io2         | ~$0.125             | Provisioned IOPS SSD     |
| st1         | ~$0.045             | Throughput optimized HDD |
| sc1         | ~$0.025             | Cold HDD                 |

---

## 🧩 Common Use Cases

| Use Case                | Example                                 |
| ----------------------- | --------------------------------------- |
| **Root volume**         | Store OS and boot files for EC2         |
| **Database storage**    | MySQL, PostgreSQL, Oracle data          |
| **Application storage** | High I/O apps like ERP systems          |
| **Backup & recovery**   | Using EBS snapshots                     |
| **Big data workloads**  | High-throughput sequential reads/writes |

---

## 🧭 Example Workflow (CLI)

```bash
# 1. Create a volume
aws ec2 create-volume \
  --availability-zone us-east-1a \
  --size 20 \
  --volume-type gp3

# 2. Attach to EC2
aws ec2 attach-volume \
  --volume-id vol-12345678 \
  --instance-id i-1234567890abcdef \
  --device /dev/sdf

# 3. Create snapshot
aws ec2 create-snapshot \
  --volume-id vol-12345678 \
  --description "Daily backup"
```

---

## 🧩 Summary

| Feature          | Description                              |
| ---------------- | ---------------------------------------- |
| **Service type** | Block-level storage for EC2              |
| **Persistence**  | Data persists beyond instance life       |
| **Performance**  | High IOPS and low latency                |
| **Backup**       | Snapshots stored in S3                   |
| **Encryption**   | KMS-based, at rest and in transit        |
| **Scalability**  | Resize and change types without downtime |

---

## 🧩 What is Amazon EFS?

**Amazon Elastic File System (EFS)** is a **fully managed, scalable, and elastic NFS (Network File System)** storage service for **Linux-based workloads** running on AWS.

It allows **multiple EC2 instances**, **containers**, or **on-premises servers** to access the **same shared file system concurrently**, making it ideal for distributed applications.

---

## ⚙️ Key Features

| Feature                        | Description                                                                                  |
| ------------------------------ | -------------------------------------------------------------------------------------------- |
| **Elastic**                    | Automatically grows or shrinks as files are added or removed — no need to provision storage. |
| **Fully managed**              | AWS handles provisioning, patching, scaling, and availability.                               |
| **Shared access**              | Can be mounted on multiple EC2 instances across Availability Zones.                          |
| **POSIX-compliant**            | Supports standard Linux file permissions, ownership, and file locking.                       |
| **Scalable**                   | Supports petabytes of data and thousands of concurrent connections.                          |
| **Durable & Highly Available** | Data is redundantly stored across multiple AZs.                                              |
| **Performance**                | Delivers consistent low latency for file operations.                                         |
| **Secure**                     | Supports encryption (at rest and in transit) and access control via IAM and security groups. |

---

## 🧱 EFS vs EBS vs S3

| Feature       | **EFS**                               | **EBS**                       | **S3**                             |
| ------------- | ------------------------------------- | ----------------------------- | ---------------------------------- |
| Storage Type  | File storage                          | Block storage                 | Object storage                     |
| Accessibility | Shared across multiple EC2s           | Attached to one EC2           | Accessed via API/URL               |
| Scaling       | Automatic                             | Manual                        | Automatic                          |
| Use Case      | Web servers, CMS, microservices       | Databases, OS disks           | Backups, archives, static assets   |
| Protocol      | NFSv4                                 | Block-level I/O               | HTTPS (REST API)                   |
| Persistence   | Persistent                            | Persistent                    | Persistent                         |
| Performance   | High throughput for concurrent access | High IOPS for single instance | Variable (depends on request size) |

---

## 🧠 How EFS Works

EFS acts as a **shared network drive** that your instances can mount via **NFS protocol**.

### Conceptually:

* You **create a file system** in your AWS region.
* AWS automatically **creates mount targets** (endpoints) in each **Availability Zone (AZ)** within a VPC.
* You **mount** the EFS file system on your EC2 instances using a standard NFS client.
* You read/write files just like in a normal Linux file system.

---

## 📦 EFS Architecture Components

### 1. **File System**

The main storage resource you create — automatically scales as data grows.

### 2. **Mount Targets**

Network interfaces in your VPC that allow EC2 instances to connect to your EFS file system (similar to “endpoints”).

Each AZ typically has one mount target.

### 3. **Access Points**

Simplifies access by defining:

* **Specific directories**
* **User and group IDs**
* **Permissions**
  for applications or users — especially useful in multi-tenant or containerized environments.

---

## 💾 EFS Storage Classes

EFS has **two storage classes** based on data access patterns:

| Storage Class                      | Description                                                       | Use Case                    |
| ---------------------------------- | ----------------------------------------------------------------- | --------------------------- |
| **EFS Standard**                   | Frequently accessed data; stored redundantly across multiple AZs. | Web content, shared configs |
| **EFS Infrequent Access (EFS-IA)** | For data that’s not frequently used; 92% cheaper than Standard.   | Backup, archival, old logs  |

> ⚙️ You can enable **Lifecycle Management** to automatically move files to EFS-IA after a period of inactivity (e.g., 30 days).

---

## ⚡ Performance Modes

EFS provides two **performance modes** depending on your workload:

| Mode                          | Description                                                 | Use Case                              |
| ----------------------------- | ----------------------------------------------------------- | ------------------------------------- |
| **General Purpose (default)** | Low latency, best for most workloads.                       | Web servers, CMS, dev environments    |
| **Max I/O**                   | Higher throughput and concurrency, slightly higher latency. | Big data, analytics, media processing |

---

## 📊 Throughput Modes

| Mode                       | Description                                            | Ideal For                                   |
| -------------------------- | ------------------------------------------------------ | ------------------------------------------- |
| **Bursting Throughput**    | Throughput scales automatically with file system size. | Most workloads                              |
| **Provisioned Throughput** | Manually set throughput (MB/s) regardless of size.     | High-performance or unpredictable workloads |

---

## 🔐 Security in EFS

EFS provides **multiple layers of security**:

### 1. **VPC Security**

* EFS is accessed within your **VPC** through mount targets.
* You can use **security groups** to restrict access (e.g., only from specific EC2 instances).

### 2. **Encryption**

* **At rest:** Managed by **AWS KMS**.
* **In transit:** Uses **TLS (Transport Layer Security)** during data transfer.

### 3. **IAM Policies**

Control which users or roles can:

* Create
* Delete
* Mount
* Modify
  EFS resources.

### 4. **POSIX Permissions**

Standard Linux permissions (UID, GID, mode bits) apply.

---

## 🧩 EFS Backup and Durability

* Data is stored **redundantly across multiple AZs**.
* EFS is designed for **11 nines (99.999999999%) durability**.
* You can enable **AWS Backup** for automated backups and retention policies.

---

## 🧰 Common Use Cases

| Use Case                             | Description                                                       |
| ------------------------------------ | ----------------------------------------------------------------- |
| **Web Server Farm**                  | Multiple EC2 instances share the same website content.            |
| **Microservices Architecture**       | Containers or Lambda functions share configuration files.         |
| **Content Management Systems (CMS)** | WordPress, Joomla, or Drupal clusters sharing uploads and themes. |
| **Machine Learning**                 | Share datasets across multiple compute nodes.                     |
| **Data Analytics / HPC**             | Store and process large data files concurrently.                  |
| **User Directories / Home Folders**  | Store per-user directories that multiple systems can access.      |

---

## 🧭 Example Setup (Step-by-step)

### 1️⃣ Create an EFS File System

* Go to **AWS Console → EFS → Create file system**
* Choose **VPC** and subnets (mount targets are auto-created).

### 2️⃣ Configure Security Groups

* Allow **NFS traffic (TCP port 2049)** between EC2 and EFS.

### 3️⃣ Mount on EC2 Instance

**Install EFS mount helper:**

```bash
sudo yum install -y amazon-efs-utils
```

**Mount using EFS DNS name:**

```bash
sudo mkdir /mnt/efs
sudo mount -t efs fs-12345678:/ /mnt/efs
```

Or automatically mount on boot by adding to `/etc/fstab`:

```
fs-12345678:/ /mnt/efs efs defaults,_netdev 0 0
```

---

## 💰 Pricing Overview

You pay for:

1. **Storage used (GB/month)** – auto-scaled.
2. **Accessed storage class** (Standard vs. IA).
3. **Data transfer** (within region is free).

| Pricing Component          | Approx. (USD/GB-month) | Notes                             |
| -------------------------- | ---------------------- | --------------------------------- |
| **Standard**               | ~$0.30                 | High availability, multi-AZ       |
| **Infrequent Access (IA)** | ~$0.025                | 92% cheaper                       |
| **Lifecycle Management**   | Free                   | Automatically moves data          |
| **Provisioned Throughput** | ~$6.00/MBps-month      | Optional, for performance control |

> 💡 You **only pay for what you use**, and capacity scales automatically.

---

## 🧩 EFS vs FSx vs EBS

| Feature       | **EFS**              | **FSx**                            | **EBS**                 |
| ------------- | -------------------- | ---------------------------------- | ----------------------- |
| Type          | Shared file storage  | Managed Windows/Linux file systems | Block storage           |
| Protocol      | NFS                  | SMB (Windows), Lustre              | Block-level             |
| Shared Access | ✅ Yes                | ✅ Yes                              | ❌ No                    |
| OS Support    | Linux                | Windows & Linux                    | Linux & Windows         |
| Typical Use   | Web apps, containers | Enterprise apps                    | Databases, boot volumes |

---

## 🧾 Summary

| Feature               | Description                        |
| --------------------- | ---------------------------------- |
| **Service Type**      | Fully managed NFS file system      |
| **Access**            | Shared across multiple EC2s        |
| **Scaling**           | Automatic (up to petabytes)        |
| **Durability**        | Multi-AZ redundant storage         |
| **Security**          | Encryption, IAM, POSIX             |
| **Performance Modes** | General Purpose, Max I/O           |
| **Storage Classes**   | Standard, Infrequent Access        |
| **Backup**            | AWS Backup or Lifecycle Management |

---

## 🔐 IAM (Identity and Access Management)

### What is AWS IAM?

**AWS Identity and Access Management (IAM)** is a web service that helps you securely control access to AWS resources. IAM allows you to manage users, groups, roles, and their permissions.

> 🔑 **Key Concept:** IAM is the **security foundation** of AWS - it answers "Who can do what?"

### Why is IAM Important?

| Benefit | Description |
|---------|-------------|
| **Security** | Control who accesses your AWS resources |
| **Granular Permissions** | Define exactly what actions are allowed |
| **No Cost** | IAM is completely free to use |
| **Centralized Control** | Manage all access from one place |
| **Compliance** | Meet security and regulatory requirements |
| **Temporary Access** | Grant time-limited permissions |

---

## 🧩 Core IAM Components

### 1. IAM Users

**An IAM User** represents a person or application that interacts with AWS.

**Characteristics:**
- Permanent, long-term credentials
- Can have username/password for Console access
- Can have access keys for programmatic access (CLI, SDK, API)
- Each user has unique permissions

**Creating a User:**
```bash
# Create IAM user
aws iam create-user --user-name john-doe

# Create login profile (console access)
aws iam create-login-profile \
  --user-name john-doe \
  --password MySecurePassword123! \
  --password-reset-required

# Create access keys (programmatic access)
aws iam create-access-key --user-name john-doe
```

**Best Practices:**
- ❌ Don't use root account for daily tasks
- ✅ Create individual IAM users for each person
- ✅ Enable MFA (Multi-Factor Authentication)
- ✅ Rotate access keys regularly
- ❌ Never share credentials

---

### 2. IAM Groups

**An IAM Group** is a collection of IAM users. Groups make it easier to manage permissions for multiple users.

**Key Points:**
- Users can belong to multiple groups
- Groups can't be nested (no groups within groups)
- Groups only contain users, not other groups

**Example Structure:**
```
Organization
├── Developers (Group)
│   ├── Alice (User)
│   ├── Bob (User)
│   └── Charlie (User)
├── Admins (Group)
│   ├── Dave (User)
│   └── Eve (User)
└── ReadOnly (Group)
    └── Frank (User)
```

**Creating a Group:**
```bash
# Create group
aws iam create-group --group-name Developers

# Add user to group
aws iam add-user-to-group \
  --group-name Developers \
  --user-name alice
```

---

### 3. IAM Roles

**An IAM Role** is an identity with specific permissions, but it's not associated with a specific user. Instead, it's **assumed** by users, applications, or services.

**Key Differences from Users:**
| Users | Roles |
|-------|-------|
| Permanent credentials | Temporary credentials |
| For people/applications | For services/temporary access |
| Long-term | Short-term (15 min to 12 hours) |

**Common Use Cases:**
- ✅ EC2 instances accessing S3
- ✅ Lambda functions accessing DynamoDB
- ✅ Cross-account access
- ✅ Federated users (SSO)
- ✅ Temporary elevated privileges

**Creating a Role for EC2:**
```bash
# Create trust policy (who can assume this role)
cat > trust-policy.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {"Service": "ec2.amazonaws.com"},
    "Action": "sts:AssumeRole"
  }]
}
EOF

# Create role
aws iam create-role \
  --role-name EC2-S3-Access \
  --assume-role-policy-document file://trust-policy.json

# Attach policy to role
aws iam attach-role-policy \
  --role-name EC2-S3-Access \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

**Attaching Role to EC2:**
```bash
# Create instance profile
aws iam create-instance-profile --instance-profile-name EC2-S3-Profile

# Add role to instance profile
aws iam add-role-to-instance-profile \
  --instance-profile-name EC2-S3-Profile \
  --role-name EC2-S3-Access

# Launch EC2 with role
aws ec2 run-instances \
  --image-id ami-abc123 \
  --instance-type t3.micro \
  --iam-instance-profile Name=EC2-S3-Profile
```

---

### 4. IAM Policies

**An IAM Policy** is a JSON document that defines permissions - what actions are allowed or denied on which resources.

**Policy Types:**

| Type | Description | Example |
|------|-------------|---------|
| **AWS Managed** | Created and maintained by AWS | `AmazonS3FullAccess` |
| **Customer Managed** | Created and maintained by you | Custom S3 policy |
| **Inline** | Embedded directly in a user/role/group | One-off permissions |

**Policy Structure:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

**Components:**
- **Version**: Policy language version (always "2012-10-17")
- **Statement**: One or more permission statements
  - **Effect**: Allow or Deny
  - **Action**: What API actions (e.g., s3:GetObject)
  - **Resource**: Which resources (ARN format)
  - **Condition** (optional): When the policy applies

**Common Policy Examples:**

**Full S3 Access:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": "s3:*",
    "Resource": "*"
  }]
}
```

**Read-Only EC2:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "ec2:Describe*",
      "ec2:Get*"
    ],
    "Resource": "*"
  }]
}
```

**Specific Bucket Access:**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "s3:ListBucket"
    ],
    "Resource": "arn:aws:s3:::my-app-bucket"
  }, {
    "Effect": "Allow",
    "Action": [
      "s3:GetObject",
      "s3:PutObject",
      "s3:DeleteObject"
    ],
    "Resource": "arn:aws:s3:::my-app-bucket/*"
  }]
}
```

**Creating and Attaching Policy:**
```bash
# Create policy
aws iam create-policy \
  --policy-name MyS3Policy \
  --policy-document file://policy.json

# Attach to user
aws iam attach-user-policy \
  --user-name alice \
  --policy-arn arn:aws:iam::123456789012:policy/MyS3Policy

# Attach to group
aws iam attach-group-policy \
  --group-name Developers \
  --policy-arn arn:aws:iam::123456789012:policy/MyS3Policy

# Attach to role
aws iam attach-role-policy \
  --role-name MyRole \
  --policy-arn arn:aws:iam::123456789012:policy/MyS3Policy
```

---

## 🔑 IAM Security Best Practices

### 1. Root Account Protection

**The Root Account** has complete access to all AWS resources.

✅ **Do:**
- Enable MFA on root account
- Lock away root access keys
- Use root only for account/billing management
- Create IAM users for daily tasks

❌ **Don't:**
- Share root credentials
- Use root for daily operations
- Create access keys for root

---

### 2. Principle of Least Privilege

Grant only the permissions required to perform a task.

**Example:**
```
❌ Bad: Give everyone AdministratorAccess
✅ Good: Give developers only EC2 and RDS access
✅ Better: Give them read-only except for dev environments
```

---

### 3. Use MFA (Multi-Factor Authentication)

**MFA** requires two forms of authentication:
1. Something you know (password)
2. Something you have (MFA device)

**Types of MFA:**
- Virtual MFA (Google Authenticator, Authy)
- Hardware MFA (YubiKey)
- SMS (not recommended, least secure)

```bash
# Enable MFA (virtual device)
aws iam enable-mfa-device \
  --user-name alice \
  --serial-number arn:aws:iam::123456789012:mfa/alice \
  --authentication-code1 123456 \
  --authentication-code2 789012
```

---

### 4. Rotate Credentials Regularly

**Access Keys:**
```bash
# Create new access key
aws iam create-access-key --user-name alice

# Update applications to use new key
# Test thoroughly

# Deactivate old key
aws iam update-access-key \
  --user-name alice \
  --access-key-id AKIAIOSFODNN7EXAMPLE \
  --status Inactive

# Delete old key (after verification)
aws iam delete-access-key \
  --user-name alice \
  --access-key-id AKIAIOSFODNN7EXAMPLE
```

**Passwords:**
- Enforce password policy
- Require password changes every 90 days
- Prevent password reuse

---

### 5. Use IAM Roles Instead of Access Keys

**For EC2 instances:**
```
❌ Bad: Store access keys in EC2
✅ Good: Use IAM role attached to EC2
```

**For Lambda functions:**
```
❌ Bad: Hardcode credentials in code
✅ Good: Use execution role
```

---

## 🔍 IAM Policy Evaluation Logic

When multiple policies apply, AWS evaluates them in this order:

```
1. Explicit DENY → Always wins
2. Explicit ALLOW → Needed for access
3. Implicit DENY → Default (no access)
```

**Example:**
```
Policy A: Allow s3:GetObject on bucket-A
Policy B: Allow s3:PutObject on bucket-A
Policy C: Deny s3:* on bucket-A

Result: DENY wins → No access to bucket-A
```

---

## 🌍 Cross-Account Access

**Scenario:** Account A needs to access resources in Account B

**Solution:** IAM Roles with Trust Relationship

**Steps:**

**Account B (Resource Account):**
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::ACCOUNT-A-ID:root"
    },
    "Action": "sts:AssumeRole"
  }]
}
```

**Account A (User Account):**
```bash
# Assume the role
aws sts assume-role \
  --role-arn arn:aws:iam::ACCOUNT-B-ID:role/CrossAccountRole \
  --role-session-name my-session
```

---

## 🧩 Common IAM Use Cases

### Use Case 1: Web Application on EC2

```
EC2 Instance → IAM Role → Access S3 and DynamoDB
```

**Setup:**
1. Create role with S3 and DynamoDB permissions
2. Attach role to EC2 instance
3. Application code uses AWS SDK (credentials automatic)

---

### Use Case 2: Developer Access

```
Developer → IAM User → Group (Developers) → Policies
```

**Setup:**
1. Create IAM user for developer
2. Add to Developers group
3. Attach policies to group (EC2, RDS, S3 read/write in dev environment)

---

### Use Case 3: Lambda Function

```
Lambda Function → Execution Role → Access DynamoDB
```

**Setup:**
1. Create role with DynamoDB permissions
2. Assign role when creating Lambda function
3. Lambda automatically uses role credentials

---

## 📊 IAM vs Other AWS Services

| Service | Purpose | Scope |
|---------|---------|-------|
| **IAM** | Authentication & authorization | AWS resources |
| **Cognito** | User authentication for apps | End-user sign-in/sign-up |
| **SSO** | Single sign-on across AWS accounts | Organization-wide access |
| **Directory Service** | Microsoft AD integration | Hybrid environments |
| **Security Token Service (STS)** | Temporary credentials | Cross-account, federated access |

---

## 🧪 IAM Policy Simulator

AWS provides a tool to test policies before applying them:

**URL:** https://policysim.aws.amazon.com/

**Use it to:**
- Test if a user can perform an action
- Debug permission issues
- Validate policies before deployment

---

## 🧾 IAM Summary

| Feature | Description |
|---------|-------------|
| **Users** | Long-term credentials for people/applications |
| **Groups** | Collections of users with shared permissions |
| **Roles** | Temporary credentials for services/federated users |
| **Policies** | JSON documents defining permissions |
| **MFA** | Additional security layer |
| **Cost** | Free service |
| **Global** | IAM is not region-specific |
| **Best Practice** | Least privilege, MFA, rotate credentials |

---

## 📚 References

### Official AWS Documentation
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Amazon RDS Documentation](https://docs.aws.amazon.com/rds/)
- [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/)
- [AWS IAM Documentation](https://docs.aws.amazon.com/iam/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)

### Learning Resources
- [AWS Free Tier](https://aws.amazon.com/free/)
- [AWS Training and Certification](https://aws.amazon.com/training/)
- [AWS Solutions Library](https://aws.amazon.com/solutions/)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [AWS This Week](https://aws.amazon.com/blogs/aws/)

### Interactive Tools
- [AWS Pricing Calculator](https://calculator.aws/)
- [AWS Well-Architected Tool](https://aws.amazon.com/well-architected-tool/)
- [IAM Policy Simulator](https://policysim.aws.amazon.com/)

### Certification Paths
- **AWS Certified Cloud Practitioner** - Foundational
- **AWS Certified Solutions Architect – Associate** - Most popular
- **AWS Certified Developer – Associate** - For developers
- **AWS Certified SysOps Administrator – Associate** - For operations

---

**Happy Learning! ☁️🚀**

## 🧩 Typical Architecture Diagram (Conceptual)

```
               ┌──────────────────────────┐
               │   Amazon EFS File System │
               └─────────────┬────────────┘
                             │
       ┌─────────────────────┴─────────────────────┐
       │                     │                     │
┌─────────────┐      ┌─────────────┐       ┌─────────────┐
│ EC2 in AZ-A │◄────►│ Mount Target│◄─────►│ EC2 in AZ-B │
└─────────────┘      └─────────────┘       └─────────────┘
       │                                          │
       └──── Shared NFS Storage (Same Data) ──────┘
```

---

Perfect — let’s go in-depth into **AWS Elastic IP (EIP)**, one of the fundamental concepts in AWS networking and EC2 management.

---

## 🧩 What is an Elastic IP (EIP)?

An **Elastic IP address** is a **static, public IPv4 address** provided by **Amazon Web Services (AWS)** that you can **associate with your AWS resources**, such as **EC2 instances**, **Network Interfaces (ENIs)**, or **NAT Gateways**.

It is called “**Elastic**” because you can **dynamically reassign** it between different instances or resources within the same AWS account and region — making it resilient to instance or hardware failures.

---

## ⚙️ Key Characteristics

| Feature                | Description                                                                                                     |
| ---------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Static IP**          | Unlike the default public IP, an Elastic IP does **not change** when an instance is stopped or restarted.       |
| **IPv4 only**          | Elastic IPs are **only available for IPv4** (IPv6 uses a different model).                                      |
| **Regional**           | Each EIP is tied to a **specific AWS region** but can be used across **Availability Zones** within that region. |
| **Reassignable**       | You can remap it to another instance or network interface quickly if one fails.                                 |
| **Publicly reachable** | Enables your EC2 instance or service to be accessed from the internet.                                          |

---

## 📶 How Elastic IP Works

Normally, when you launch an EC2 instance with a public IP:

* AWS assigns a **dynamic public IP address**.
* This IP changes whenever you stop and start the instance.

With an **Elastic IP**, you:

1. **Allocate** a fixed public IP from AWS’s pool.
2. **Associate** it with your EC2 instance or Elastic Network Interface (ENI).
3. The EIP now provides a **permanent public address** for your resource.

If your instance fails:

* You can **disassociate** the EIP from the failed instance.
* And **associate** it with another instance — ensuring continuity of service without DNS updates.

---

## 🧱 Elastic IP Lifecycle

| Step                  | Description                                                               |
| --------------------- | ------------------------------------------------------------------------- |
| **1. Allocation**     | AWS assigns an Elastic IP to your AWS account in a region.                |
| **2. Association**    | You link (associate) it with an EC2 instance or ENI.                      |
| **3. Disassociation** | You remove the EIP association from a resource.                           |
| **4. Release**        | You return the EIP back to AWS’s public IP pool (no longer owned by you). |

---

## 🧭 Example Workflow (CLI)

### 1️⃣ Allocate an Elastic IP

```bash
aws ec2 allocate-address --domain vpc
```

Response:

```json
{
  "PublicIp": "54.210.123.45",
  "AllocationId": "eipalloc-123abc456def7890"
}
```

### 2️⃣ Associate with an EC2 Instance

```bash
aws ec2 associate-address \
  --instance-id i-0abcdef1234567890 \
  --allocation-id eipalloc-123abc456def7890
```

### 3️⃣ Disassociate it

```bash
aws ec2 disassociate-address --association-id eipassoc-0abc123def456
```

### 4️⃣ Release it

```bash
aws ec2 release-address --allocation-id eipalloc-123abc456def7890
```

---

## 🧠 EIP vs Normal Public IP

| Feature                  | Dynamic Public IP | Elastic IP    |
| ------------------------ | ----------------- | ------------- |
| Changes after stop/start | ✅ Yes             | ❌ No          |
| Allocated automatically  | ✅ Yes             | ❌ No (manual) |
| Reassignable             | ❌ No              | ✅ Yes         |
| Static for DNS mapping   | ❌ No              | ✅ Yes         |
| Charge when not in use   | ❌ No              | ✅ Yes         |

---

## 💰 Pricing Details

Elastic IPs are **mostly free when in use** but **charged when idle**:

| Scenario                           | Cost                                   |
| ---------------------------------- | -------------------------------------- |
| Associated with a running instance | ✅ Free (1 per instance)                |
| Allocated but **not associated**   | 💲 ~$0.005 per hour                    |
| More than 1 EIP per instance       | 💲 Additional charge                   |
| Data transfer                      | Standard EC2 data transfer costs apply |

> 💡 **Best practice:** Always release unused Elastic IPs — AWS charges for idle ones to encourage efficient use of IPv4 space.

---

## 🧩 Elastic IP Association Scenarios

### 1. **EC2 Instance**

Attach directly to an instance’s primary network interface.

### 2. **Elastic Network Interface (ENI)**

Attach to a specific **network interface** — recommended for flexibility (especially in multi-NIC setups).

### 3. **NAT Gateway**

Used to give internet access to private subnets.

### 4. **Load Balancer**

Elastic IPs are **not directly attached** to Load Balancers — AWS manages their public IPs internally.

---

## 🔒 Security and Access Control

* Elastic IPs use **VPC Security Groups** to control inbound/outbound traffic.
* Access to allocate, associate, or release EIPs can be controlled via **IAM policies**.
* Example IAM permissions:

```json
{
  "Effect": "Allow",
  "Action": [
    "ec2:AllocateAddress",
    "ec2:AssociateAddress",
    "ec2:ReleaseAddress"
  ],
  "Resource": "*"
}
```

---

## 🧰 Common Use Cases

| Use Case                         | Description                                                      |
| -------------------------------- | ---------------------------------------------------------------- |
| **Web Server**                   | Keep the same public IP even after instance restarts or scaling. |
| **DNS Mapping**                  | Use a static IP in DNS records for stable public endpoints.      |
| **Failover / High Availability** | Quickly remap EIP to a standby instance if the primary fails.    |
| **VPN Endpoints**                | Maintain fixed IPs for remote access configurations.             |
| **Bastion Host**                 | Provide a permanent IP for SSH/RDP access in secure VPCs.        |

---

## ⚡ Best Practices

1. ✅ **Use EIPs only when needed.** For scalable architectures, prefer Elastic Load Balancers (ELBs) or Route 53 with domain names.
2. ✅ **Release unused EIPs** to avoid costs and conserve IPv4 addresses.
3. ✅ **Use DNS CNAMEs or Route 53 records** that map to EIPs for flexibility.
4. ✅ **Monitor EIP usage** with CloudWatch or AWS Config.
5. ✅ **Automate EIP re-association** during instance recovery (using scripts or AWS Lambda).

---

## 🧾 Summary

| Feature         | Description                                              |
| --------------- | -------------------------------------------------------- |
| **Type**        | Static public IPv4 address                               |
| **Scope**       | Regional (can be used across AZs)                        |
| **Persistence** | Remains fixed until released                             |
| **Elasticity**  | Can be reassigned to other instances instantly           |
| **Pricing**     | Free when in use; charged when idle                      |
| **Use Case**    | High-availability servers, DNS mapping, failover systems |

---

## 🧩 Visual Example (Conceptual)

```
          ┌──────────────────────────┐
          │  Elastic IP: 54.210.X.X  │
          └────────────┬─────────────┘
                       │
        ┌──────────────┴──────────────┐
        │                             │
┌────────────────┐             ┌────────────────┐
│ EC2 Instance A │             │ EC2 Instance B │
│ (Primary)      │ ◄─────────► │ (Backup)       │
└────────────────┘   Reassign  └────────────────┘
         ▲
         │
         │ (If A fails, EIP remapped to B)
```

---
