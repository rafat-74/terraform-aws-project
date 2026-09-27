<div align="center">

# 🌐 Terraform AWS Project: Secure Multi-Tier VPC with NAT Gateway

<img src="https://img.shields.io/badge/IaC-Terraform_1.13+-7B42BC?style=for-the-badge&logo=terraform&logoColor=white"/>
<img src="https://img.shields.io/badge/Cloud-AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white"/>
<img src="https://img.shields.io/badge/Network-VPC-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white"/>
<img src="https://img.shields.io/badge/Compute-Amazon_EC2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white"/>
<img src="https://img.shields.io/badge/Security-Private_Subnets-10B981?style=for-the-badge&logo=shield&logoColor=white"/>

<br/><br/>

> **An automated, production-grade AWS VPC network architecture provisioned with Terraform (IaC). Enforces security boundaries by isolating critical compute workloads inside private subnets with controlled outbound internet connectivity via an Elastic IP-backed NAT Gateway.**

</div>

---

## 📌 Architecture Overview

This project provides a robust, reusable Terraform blueprint to deploy a standard multi-tier AWS network following AWS Well-Architected security best practices:

- **Isolated Private Compute:** Application servers and databases reside inside private subnets completely inaccessible from the public internet.
- **Controlled Egress via NAT Gateway:** Outbound-only connectivity enables private instances to pull operating system updates and external dependencies safely.
- **Segregated Routing:** Separate route tables enforce clean traffic isolation between public ingress and private egress flows.
- **Verification Instance:** Includes an automated Amazon Linux 2023 EC2 deployment within the private subnet to validate outbound routing and NAT Gateway functionality.

---

## 🏗️ Network Topology Diagram

```
                        Internet (0.0.0.0/0)
                                 ▲
                                 │
                     ┌───────────▼───────────┐
                     │   Internet Gateway    │
                     └───────────┬───────────┘
                                 │
┌────────────────────────────────┼────────────────────────────────┐
│  AWS VPC: 172.16.0.0/16        │                                │
│                                │                                │
│   ┌────────────────────────────▼────────────────────────────┐   │
│   │ PUBLIC SUBNET (172.16.2.0/24)                           │   │
│   │                                                         │   │
│   │   [ Elastic IP ] ──► [ NAT Gateway ]                    │   │
│   │                             ▲                           │   │
│   └─────────────────────────────┼───────────────────────────┘   │
│                                 │ (Private Outbound Egress)     │
│   ┌─────────────────────────────┴───────────────────────────┐   │
│   │ PRIVATE SUBNET (172.16.1.0/24)                          │   │
│   │                                                         │   │
│   │   [ Private EC2 Instance: Amazon Linux 2023 (t3.micro) ]│   │
│   │   (No Public IP · Secure Outbound via NAT Gateway)      │   │
│   └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ AWS Resources Provisioned

Deployed in the **`eu-north-1`** (Stockholm) region:

| Resource Type | Resource Identifier | CIDR / Configuration | Description |
|---|---|---|---|
| **VPC** | `aws_vpc` | `172.16.0.0/16` | Main virtual private cloud container with DNS hostnames enabled |
| **Public Subnet** | `aws_subnet.public` | `172.16.2.0/24` | Mapped to the Internet Gateway for public ingress/egress |
| **Private Subnet** | `aws_subnet.private` | `172.16.1.0/24` | Isolated compute tier without public IPv4 addressing |
| **Internet Gateway** | `aws_internet_gateway` | Direct VPC Attachment | Facilitates bidirectional internet communication for public resources |
| **NAT Gateway** | `aws_nat_gateway` | Hosted in Public Subnet | Outbound internet proxy for private instances using dedicated Elastic IP |
| **Elastic IP** | `aws_eip` | Static AWS Public IPv4 | Dedicated static IP allocated for the NAT Gateway |
| **Route Tables** | `aws_route_table` | Public & Private tables | Independent routing rules for public and private subnets |
| **Validation EC2** | `aws_instance` | `t3.micro` | Amazon Linux 2023 instance in private subnet verifying NAT egress |

---

## ⚙️ Prerequisites

1. **Terraform CLI:** `>= 1.13.4` (or modern Terraform 1.5+)
2. **AWS CLI:** Authenticated via `aws configure` with permissions for EC2, VPC, and networking resources.

---

## 🚀 Deployment Instructions

### 1. Initialize Working Directory
Downloads the required AWS providers and sets up the local state:

```bash
terraform init
```

### 2. Validate and Plan Execution
Review the resources Terraform intends to provision:

```bash
terraform plan
```

### 3. Provision Infrastructure
Apply the configuration to AWS:

```bash
terraform apply -auto-approve
```

### 4. Verify NAT Gateway Outbound Connectivity
Connect to the private instance (via AWS Systems Manager Session Manager or Bastion host) and verify internet access without a public IP:

```bash
# Verify DNS and external outbound routing
curl -I https://aws.amazon.com
```

### 5. Tear Down / Destroy
To clean up all provisioned resources and prevent cloud charges:

```bash
terraform destroy -auto-approve
```

---

## 🔒 Security Best Practices Implemented

- **No Inbound Exposure:** The private subnet has zero direct routes to the Internet Gateway.
- **Controlled Outbound:** The private route table routes `0.0.0.0/0` exclusively through the NAT Gateway.
- **Least-Privilege Networking:** Subnet segregation prevents lateral movement across application and database tiers.

---

## 📬 Author & Connect

<div align="center">

**Developed by Rafat Ashraf**  
*Cloud & DevOps Engineer*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/rafat-devops)

</div>
