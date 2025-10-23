# 🚀 Terraform AWS Project: Secure VPC Network with NAT Gateway

## 📝 Overview

This project utilizes **Terraform** to provision a complete and secure network infrastructure on **Amazon Web Services (AWS)**. The design implements best practices by separating resources into distinct **Public** and **Private Subnets** within a custom **Virtual Private Cloud (VPC)**.

**The Goal:** To create a robust network where public-facing resources (e.g., Load Balancers, not included here) reside in the Public Subnet, and protected resources (e.g., Application Servers, Databases) are isolated in the Private Subnet, with outbound-only internet access granted via a **NAT Gateway**.

---

## 🛠️ AWS Resources Provisioned

The following resources are deployed in the **`eu-north-1`** (Stockholm) region:

* **VPC (`aws_vpc`):** The primary network container with CIDR block **`172.16.0.0/16`**.
* **Subnets (`aws_subnet`):**
    * **Public Subnet:** CIDR **`172.16.2.0/24`**, mapped to the Internet Gateway.
    * **Private Subnet:** CIDR **`172.16.1.0/24`**, routed through the NAT Gateway.
* **Internet Gateway (`aws_internet_gateway`):** Enables internet access for resources in the Public Subnet.
* **NAT Gateway (`aws_nat_gateway`):** Placed in the Public Subnet, allowing resources in the Private Subnet to access the internet (e.g., for updates) without being publicly reachable. Uses an **Elastic IP (`aws_eip`)**.
* **Route Tables (`aws_route_table`):** Dedicated Route Tables for both Public and Private traffic flows.
* **EC2 Instance (`aws_instance`):** A **`t3.micro`** instance (running **Amazon Linux 2023**) deployed in the **Private Subnet** to validate the NAT Gateway's outbound connectivity.

---

## ⚙️ Prerequisites

To deploy this infrastructure, you must have the following tools and configurations:

1.  **Terraform CLI:** Required version is **`>= 1.13.4`**.
2.  **AWS CLI Configured:** Your AWS credentials must be configured locally (e.g., via `~/.aws/credentials` or **Environment Variables** (متغيرات البيئة)) with sufficient permissions to create the listed resources.

---

## 🚀 Deployment Steps

Navigate to the project directory in your **CLI** (واجهة سطر الأوامر) and run the following commands:

### 1. Initialization
Initialize the project and download the necessary **AWS Provider** (مزود):

```bash
terraform init