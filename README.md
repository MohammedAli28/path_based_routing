# path_based_routing
# 🚀 AWS Path-Based Routing Project (DevOps Hands-On)

## 📌 Overview

This project demonstrates how to build a **highly available and secure AWS architecture** using a custom VPC, public and private subnets, a bastion host, and an Application Load Balancer with **path-based routing**.

The setup routes traffic to different backend servers based on URL paths like `/aws`, `/azure`, `/gcp`.

---

## 🏗️ Architecture

```
Internet
   ↓
Application Load Balancer (Public Subnets)
   ↓
Target Groups (Path-Based Routing)
   ↓
Private EC2 Instances (Apache Servers)
   ↑
Bastion Host (for SSH access)
```

---

## 🧱 Infrastructure Setup

### 1️⃣ VPC & Networking

* Create a **VPC**
* Create and attach **Internet Gateway (IGW)**
* Create **NAT Gateway** (for outbound internet access from private subnets)

---

### 2️⃣ Subnets

#### Public Subnets (for Load Balancer)

* 2 Public Subnets

  * AZ 1A
  * AZ 1B

#### Private Subnets (for backend servers)

* 4 Private Subnets

  * 2 in AZ 1A
  * 2 in AZ 1B

---

### 3️⃣ Route Tables

#### Public Route Table

* Add route:

  ```
  0.0.0.0/0 → Internet Gateway
  ```
* Associate with:

  * Public Subnet 1A
  * Public Subnet 1B

#### Private Route Table

* Add route:

  ```
  0.0.0.0/0 → NAT Gateway
  ```
* Associate with:

  * All 4 Private Subnets

---

## 🔐 Security Groups

### 🔹 Load Balancer SG

* Allow:

  * HTTP (80) → 0.0.0.0/0
  * HTTPS (443) → 0.0.0.0/0

---

### 🔹 Bastion Host SG

* Allow:

  * SSH (22) → Your IP only

---

### 🔹 Private Servers SG

* Allow:

  * HTTP (80) → Load Balancer SG
  * SSH (22) → Bastion SG

---

## 🖥️ EC2 Instances

### 🔹 Bastion Host

* Placed in Public Subnet
* Used to access private servers via SSH

---

### 🔹 Private Servers (4 Instances)

* Hosted in Private Subnets:

  * `home-page`
  * `aws`
  * `azure`
  * `gcp`

---

## ⚙️ Server Configuration

After logging into Bastion:

### Connect to private servers:

```bash
ssh ec2-user@<private-ip>
```

### Install Apache:

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

---

### Update index.html

Example:

```html
<h1>Welcome to AWS Server</h1>
```

Repeat for each server:

* Home Page
* AWS
* Azure
* GCP

---

## 🎯 Target Groups

Create **4 Target Groups**:

| Target Group | Server    |
| ------------ | --------- |
| home-tg      | Home Page |
| aws-tg       | AWS       |
| azure-tg     | Azure     |
| gcp-tg       | GCP       |

* Protocol: HTTP
* Port: 80
* Register respective EC2 instances

---

## 🌐 Load Balancer Setup

Create an:

* Application Load Balancer
* Scheme: Internet-facing
* Subnets: Public Subnets (1A & 1B)

---

### Listener Configuration

#### Default Rule

* Forward to: `home-tg`

---

### Path-Based Routing Rules

| Path       | Target Group |
| ---------- | ------------ |
| `/aws/*`   | aws-tg       |
| `/azure/*` | azure-tg     |
| `/gcp/*`   | gcp-tg       |

---

## 🌍 Access Application

Use Load Balancer DNS:

```
http://<ALB-DNS>/
http://<ALB-DNS>/aws/
http://<ALB-DNS>/azure/
http://<ALB-DNS>/gcp/
```

---

## 🔄 Final Step

Update your `index.html` (home page) with links:

```html
<a href="http://<ALB-DNS>/aws">AWS/</a>
<a href="http://<ALB-DNS>/azure">Azure/</a>
<a href="http://<ALB-DNS>/gcp">GCP/</a>
```

---

## ✅ Key Concepts Covered

* VPC & Subnet Design
* Public vs Private Architecture
* Bastion Host Access
* NAT Gateway (Outbound Internet)
* Security Groups Best Practices
* Target Groups & Health Checks
* Path-Based Routing using ALB

---

## 🚀 Outcome

A fully working **multi-tier architecture** where:

* Users access via Load Balancer
* Traffic is routed based on URL path
* Backend servers are secure in private subnets

---

## 📌 Notes

* Do NOT expose private instances to the internet
* Always restrict SSH access via Bastion
* Ensure health checks return HTTP 200
* Verify Security Group rules if traffic fails

---

## 🙌 Ali

DevOps Hands-On Practice Project
