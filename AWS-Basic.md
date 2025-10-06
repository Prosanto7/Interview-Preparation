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
