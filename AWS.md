## 🧩 What is AWS EC2?

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
