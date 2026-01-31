#  🔐 AWS-Site-to-Site-VPN-Setup

## 📘 📖 Project Overview

### 🎯 Objective
Design and implement a secure, encrypted, and highly available private network connection between two AWS regions using AWS Site-to-Site VPN.

The goal is to enable:

- ✅ Private inter-VPC communication
- ✅ Cross-region application access
- ✅ Encrypted IPsec tunnels over the internet
- ✅ Low-cost alternative to Direct Connect
- ✅ High availability with dual tunnels

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 🧱 Architecture diagram


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 📌 Prerequisites

Before beginning, ensure you have:

- AWS Console access with IAM permissions to create VPCs, VPNs, gateways, route tables, and EC2 instances.
- Two AWS regions enabled:
   - ap-south-1 (Mumbai)
   - us-east-1 (N. Virginia)
- CIDR blocks planned that do not overlap (e.g., 10.1.0.0/16 and 10.2.0.0/16).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 1. Create VPC — Mumbai Region (ap-south-1)

1. Sign in to the AWS Management Console → VPC Dashboard.
2. Click Create VPC:
   - Name Tag: AWS_SITE_VPC
   - IPv4 CIDR block: 10.1.0.0/16
   - Tenancy: Default
3. Create a subnet in ap-south-1a:
   - Subnet Name: AWS_SITE_SUBNET
   - CIDR block: 10.1.0.0/24
4. Create and attach an Internet Gateway:
   - Name: AWS_SITE_IGW
   - Attach to AWS_SITE_VPC.
5. Add routes to allow outbound internet:
   - Destination: 0.0.0.0/0
   - Target: AWS_SITE_IGW.
6. Associate route table with AWS_SITE_SUBNET

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 2. Create VPC — N. Virginia Region (us-east-1)

Repeat VPC setup steps for the second region:

1. VPC Name: AWS_Customer_VPC
2. CIDR block: 10.2.0.0/16
3. Subnet (AWS_Customer_SUBNET) in us-east-1a (CIDR: 10.2.0.0/24).
4. Create and attach Internet Gateway (AWS_Customer_IGW).
5. Add 0.0.0.0/0 → AWS_Customer_IGW route.
6. Associate route table with AWS_Customer_SUBNET.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 3. Launch EC2 Instance —  Mumbai Region (ap-south-1)

This instance is used for connectivity testing:

1. Navigate to EC2 → Launch Instance.
2. Pick Amazon Linux 2 (or equivalent).
3. Instance type: t2.micro (for testing).
4. Network: AWS_SITE_VPC.
5. Enable Auto-assign Public IP.
6. Create SSH key pair (if needed).
7. Create a Security Group allowing:
   - SSH (22)
   - ICMP (ping)
   - Any necessary application traffic
8. Launch.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 4. Launch EC2 Instance — N. Virginia Region (us-east-1)

1. Navigate to EC2 → Launch Instance.
2. Pick Amazon Linux 2 (or equivalent).
3. Instance type: 't2.micro' (for testing).
4. Network: AWS_Customer_VPC.
5. Enable Auto-assign Public IP.
6. Create SSH key pair (if needed).
7. Create a Security Group allowing:
   - SSH (22)
   - ICMP (ping)
   - Any necessary application traffic
8. Launch.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 5. Create Virtual Private Gateway — Mumbai Region

1. VPC Dashboard → Virtual Private Gateways → Create.
2. Name: AWS_SITE_VPG.
3. Attach it to the AWS_SITE_VPC.
4. Confirm status is Attached.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 6. Create Customer Gateway

A Customer Gateway represents the remote network endpoint:

1. In the VPC Console, go to Customer Gateways → Create.
2. Name: Customer_AWS_S2S.
3. Routing: Static.
4. IP Address: The public IP of the EC2 instance in us-east-1
   - (or the public fixed IP of your on-prem router/firewall if available).
5. Save.

⚠️ If your remote endpoint uses BGP, choose Dynamic and specify ASN. Otherwise, static routing covers most cross-VPC scenarios.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 7. Enable Route Propagation — Mumbai VPC

1. Go to Route Tables for AWS_SITE_VPC.
2. Edit Route Propagation and enable it for AWS_SITE_VPG.
3. This allows AWS to automatically insert VPN routes.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


