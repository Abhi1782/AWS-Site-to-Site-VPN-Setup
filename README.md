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

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 🧱 Architecture diagram


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 📌 Prerequisites

Before beginning, ensure you have:

- AWS Console access with IAM permissions to create VPCs, VPNs, gateways, route tables, and EC2 instances.
- Two AWS regions enabled:
   - ap-south-1 (Mumbai)
   - us-east-1 (N. Virginia)
- CIDR blocks planned that do not overlap (e.g., 10.1.0.0/16 and 10.2.0.0/16).

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
