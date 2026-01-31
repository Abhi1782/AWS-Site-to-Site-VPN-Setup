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
  
<img width="1366" height="768" alt="Screenshot (200)" src="https://github.com/user-attachments/assets/35e282b4-e384-4fd7-b761-00a54a13a223" />

<img width="1366" height="768" alt="Screenshot (199)" src="https://github.com/user-attachments/assets/89baf672-9c92-4406-8b42-ca1e9b584d13" />

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

## 8. Create Site-to-Site VPN Connection

1. In VPC Console → Site-to-Site VPN Connections → Create.
2. Name Tag: Mumbai-to-Virginia–VPN.
3. Target Gateway: AWS_SITE_VPG.
4. Customer Gateway: Customer_AWS_S2S.
5. Routing: Static.
6. Add your remote CIDR:
   - 10.2.0.0/16 (us-east-1 VPC).
7. Create.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 9. Download VPN Configuration

1. In Site-to-Site VPN Connections, select the new VPN.
2. Click Download Configuration and select your device/vendor.
3. Save config for phase-1/phase-2 settings.
4. Apply configuration to your Customer Gateway device (router/firewall).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 10. Access EC2 Instance launched in (N.Virginia Region)

### Install Libreswan

1. Ubuntu/Debian
   
         sudo apt update
         sudo apt install libreswan -y

2. RHEL/CentOS/Amazon Linux

         sudo yum install libreswan -y

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 11. Configure Libreswan

🔹 Modify /etc/ipsec.conf:

      vi /etc/ipsec.conf

      include /etc/ipsec.d/*.conf

🔹 Update /etc/sysctl.conf:

Next Steps:

🔹 vi /etc/sysctl.conf

      net.ipv4.ip_forward = 1
      net.ipv4.conf.all.accept_redirects = 0
      net.ipv4.conf.all.send_redirects = 0

🔹 Restart network service:

      systemctl restart systemd-networkd

🔹 Create/Edit /etc/ipsec.d/aws-vpn.conf:

      conn Tunnel1
        authby=secret
        auto=start
        left=%defaultroute # Assuming this is your local subnet, replace if needed
        leftid=54.85.16.212
        right=3.6.59.5
        type=tunnel
        ikelifetime=8h
        keylife=1h
        # Consider stronger options if supported by both sides:
        # phase2alg=aes256-gcm-sha256
        # ike=aes256-gcm-sha256
        # keyingtries=%forever
        keyexchange=ike
        leftsubnet=10.2.0.0/16
        rightsubnet=10.1.0.0/16
        dpddelay=10
        dpdtimeout=30
        dpdaction=restart_by_peer

 🔹 Create/Edit /etc/ipsec.d/aws-vpn.secrets:

       vi /etc/ipsec.d/aws-vpn.secrets

      customer_public_ip aws_vgw_public_ip : PSK "shared secret"

      54.85.16.212 3.6.59.5: PSK "6hCv4ZehxKhhfb8cNfC4z4NBicyod_W6"    

🔹 Set Permissions
         
         sudo chmod 600 /etc/ipsec.secrets

Next Steps:

🔹 Start Libreswan Service:

      chkconfig ipsec on
      sudo systemctl start ipsec
      sudo systemctl enable ipsec

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 12. Verify Tunnel Status on AWS Console


## 13. Test Connectivity

   - From Linux side

         ping 10.1.0.10

   - From AWS EC2
 
         ping 10.2.0.10

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<p align="center">
  <strong>HAPPY LEARNING</strong><br>
  <em>© Abhishek Prajapati | DevOps & Cloud Engineer</em><br>
</p>

   
