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

![Site to Site VPN](https://github.com/user-attachments/assets/592b6d24-7701-42b4-a5af-d16645f0025b)

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

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (199)" src="https://github.com/user-attachments/assets/89baf672-9c92-4406-8b42-ca1e9b584d13" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

3. Create a subnet in ap-south-1a:
   - Subnet Name: AWS_SITE_SUBNET
   - CIDR block: 10.1.0.0/24

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (200)" src="https://github.com/user-attachments/assets/20d504a2-2b40-4da4-a8ad-748ce4d73ea7" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (201)" src="https://github.com/user-attachments/assets/44985c7b-d0c3-43e3-8513-1b26d42dcd64" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4. Create and attach an Internet Gateway:
   - Name: AWS_SITE_IGW
   - Attach to AWS_SITE_VPC.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (202)" src="https://github.com/user-attachments/assets/49f78097-07da-49ca-875d-2939942f507b" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (203)" src="https://github.com/user-attachments/assets/c9558f64-001b-45f4-9af7-e9aa65f17be7" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (204)" src="https://github.com/user-attachments/assets/d8bc7901-7eb0-4eb0-ae68-bd8d67121623" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. Add routes to allow outbound internet:
   - Destination: 0.0.0.0/0
   - Target: AWS_SITE_IGW.
  
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (205)" src="https://github.com/user-attachments/assets/f182e24e-3145-453b-9f29-0f61167b56f9" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (206)" src="https://github.com/user-attachments/assets/4590ee01-1ed1-4759-82df-ec238d65d1ea" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

6. Associate the route table with AWS_SITE_SUBNET

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (208)" src="https://github.com/user-attachments/assets/eebee967-2423-425a-b555-381185a82f10" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 2. Create VPC — N. Virginia Region (us-east-1)

Repeat VPC setup steps for the second region:

1. VPC Name: AWS_Customer_VPC
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (209)" src="https://github.com/user-attachments/assets/991c01e7-b7c0-466b-bf4d-6119e41806be" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (210)" src="https://github.com/user-attachments/assets/68b3df09-b0b2-4587-87e9-4761eca23ff8" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2. CIDR block: 10.2.0.0/16
3. Subnet (AWS_Customer_SUBNET) in us-east-1a (CIDR: 10.2.0.0/24).
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (211)" src="https://github.com/user-attachments/assets/1064968c-93c3-4d70-b859-f51e96781fec" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (212)" src="https://github.com/user-attachments/assets/a50c6e8b-2e70-46df-8d46-86efcbd1bb74" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4. Create and attach Internet Gateway (AWS_Customer_IGW).
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (215)" src="https://github.com/user-attachments/assets/11dea9a3-f472-45f5-829a-e225d2000939" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (216)" src="https://github.com/user-attachments/assets/b5c7e6c9-b7d3-4b61-96ed-d9a0600f61f9" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
5. Add 0.0.0.0/0 → AWS_Customer_IGW route.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (220)" src="https://github.com/user-attachments/assets/200066b0-5dfb-4f6c-9114-5639e774620c" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
6. Associate route table with AWS_Customer_SUBNET.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (217)" src="https://github.com/user-attachments/assets/9b6e1897-1756-4e59-b4a0-eba8d0bbd50c" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (218)" src="https://github.com/user-attachments/assets/6526527f-795d-4460-aefd-257b648c5c61" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (221)" src="https://github.com/user-attachments/assets/51f1d477-5fd7-45d5-81fb-292f30f2fc6b" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (222)" src="https://github.com/user-attachments/assets/3f242370-822b-49c1-88e4-e06b1469a472" />

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

<img width="1366" height="768" alt="Screenshot (224)" src="https://github.com/user-attachments/assets/6c3ef2a6-9da6-4c7a-a42c-422450a6020f" />

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

<img width="1366" height="768" alt="Screenshot (223)" src="https://github.com/user-attachments/assets/f2e00591-0c1d-4035-8ff0-4da426072de1" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 5. Create Virtual Private Gateway — Mumbai Region

1. VPC Dashboard → Virtual Private Gateways → Create.
2. Name: AWS_SITE_VPG.
3. Attach it to the AWS_SITE_VPC.
4. Confirm status is Attached.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (225)" src="https://github.com/user-attachments/assets/7883bd74-74f5-4f21-9242-65a7065523dc" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (226)" src="https://github.com/user-attachments/assets/7678f4b9-d3c8-4616-948c-54bcdadc815e" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (227)" src="https://github.com/user-attachments/assets/e0d0c886-9883-48a2-9d13-841bc980827f" />

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

<img width="1366" height="768" alt="Screenshot (228)" src="https://github.com/user-attachments/assets/7050c5cc-517a-4241-8db6-81dc4c824a6f" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (229)" src="https://github.com/user-attachments/assets/15f133e1-c591-4f65-8462-c1a23f5ebe20" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 7. Enable Route Propagation — Mumbai VPC

1. Go to Route Tables for AWS_SITE_VPC.
2. Edit Route Propagation and enable it for AWS_SITE_VPG.
3. This allows AWS to automatically insert VPN routes.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (230)" src="https://github.com/user-attachments/assets/b52cf2fd-4573-4ee1-8527-d79f6777eaa6" />

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

<img width="1366" height="768" alt="Screenshot (231)" src="https://github.com/user-attachments/assets/44509be3-46ba-4155-8ffb-0a4611acc60b" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (232)" src="https://github.com/user-attachments/assets/2faccaea-db05-4a88-934c-ebaf9e580782" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 9. Download VPN Configuration

1. In Site-to-Site VPN Connections, select the new VPN.
2. Click Download Configuration and select your device/vendor.
3. Save config for phase-1/phase-2 settings.
4. Apply configuration to your Customer Gateway device (router/firewall).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (233)" src="https://github.com/user-attachments/assets/e2a6e6a6-b9a3-4658-b515-e65cf6cd3a0c" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 10. Access EC2 Instance launched in (N.Virginia Region)

### Install Libreswan

1. Ubuntu/Debian
   
         sudo apt update
         sudo apt install libreswan -y

2. RHEL/CentOS/Amazon Linux

         sudo yum install libreswan -y

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (234)" src="https://github.com/user-attachments/assets/2f247851-d1b4-4899-b768-9fabfa38efaa" />

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

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (235)" src="https://github.com/user-attachments/assets/91253b74-7a2c-4da1-8a2d-48709605ee62" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

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

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (236)" src="https://github.com/user-attachments/assets/889a99c6-6a07-460b-ada3-560a984efad3" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 🔹 Create/Edit /etc/ipsec.d/aws-vpn.secrets:

       vi /etc/ipsec.d/aws-vpn.secrets

      customer_public_ip aws_vgw_public_ip : PSK "shared secret"

      54.85.16.212 3.6.59.5: PSK "6hCv4ZehxKhhfb8cNfC4z4NBicyod_W6"    

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (237)" src="https://github.com/user-attachments/assets/3a7fa489-988c-465b-9972-5b7980a287d5" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
🔹 Set Permissions
         
         sudo chmod 600 /etc/ipsec.secrets

Next Steps:

🔹 Start Libreswan Service:

      chkconfig ipsec on
      sudo systemctl start ipsec
      sudo systemctl enable ipsec

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (238)" src="https://github.com/user-attachments/assets/0b9754e1-70a9-446d-be28-24a4b924d767" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 12. Verify Tunnel Status on AWS Console

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (239)" src="https://github.com/user-attachments/assets/3b674192-5ac5-443e-b455-fb208f8834da" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


## 13. Test Connectivity

   - From the Linux side

         ping 10.1.0.10

   - From AWS EC2
 
         ping 10.2.0.10

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1366" height="768" alt="Screenshot (240)" src="https://github.com/user-attachments/assets/83152aa7-244b-4d29-9388-49334fd90660" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<p align="center">
  <strong>HAPPY LEARNING</strong><br>
  <em>© Abhishek Prajapati | DevOps & Cloud Engineer</em><br>
</p>

