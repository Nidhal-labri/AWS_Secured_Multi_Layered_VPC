# 🛡️ AWS Project: Deploying a Secured Multi-Layered VPC

This project demonstrates how I deployed a **secured multi-layered VPC on AWS** using **Network ACLs (NACLs)** and **Security Groups** to implement layered security for public and private resources.

---

## 🗺️ Architecture Diagram

<img width="4071" height="2106" alt="image" src="https://github.com/user-attachments/assets/cb821e94-9438-4d23-a5bb-266e47986fd4" />

---

## 🧱 Key AWS Services Used

- **VPC**: To define the isolated network environment.
- **Subnets**: Separate public and private resources within the VPC.
- **Internet Gateway (IGW)**: Enables internet access for public subnets.
- **Route Tables**: To control routing within the VPC.
- **Security Groups**: Instance-level virtual firewalls.
- **Network ACLs**: Subnet-level stateless firewalls for additional security.
- **EC2**: Compute resources for deploying instances.
- **Key Pairs**: For secure SSH access.

---

## 🛠️ Deployment Steps

### ✅ Step 1 – Creating the VPC

Created a VPC named `my_VPC` with:
- IPv4 CIDR block: `10.0.0.0/16`
- No IPv6 CIDR block

### ✅ Step 2 – Attaching an Internet Gateway

- Created an Internet Gateway named `my_IGW`
- Attached it to `my_VPC`

### ✅ Step 3 – Creating Two Subnets

Created two subnets in `my_VPC`:
- `public_subnet`:  
  - CIDR block: `10.0.1.0/24`  
  - Availability Zone: No preference  
- `private_subnet`:  
  - CIDR block: `10.0.2.0/24`  
  - Availability Zone: No preference  

### ✅ Step 4 – Route Tables & Routes

- Created two route tables:
  - `public_route` associated with `public_subnet`
    - Route: `0.0.0.0/0` → Target: `my_IGW`
  - `private_route` associated with `private_subnet`

🗺️ **VPC Resource Map** 
<img width="1646" height="355" alt="13" src="https://github.com/user-attachments/assets/7ba9d64d-ce87-4583-904f-79e1793ba2bf" />

### ✅ Step 5 – Creating a Security Group

Created a security group named `my_securitygroup` with the following **inbound rules**:

| Type               | Protocol | Port Range | Source      |
|--------------------|----------|------------|-------------|
| SSH                | TCP      | 22         | 0.0.0.0/0   |
| All ICMP - IPv4    | ICMP     | ALL        | 0.0.0.0/0   |

<img width="1641" height="623" alt="image" src="https://github.com/user-attachments/assets/947214a5-27b5-46c2-a5d9-2ef7695c7429" />

### ✅ Step 6 – Creating & Configuring Network ACL

Created a Network ACL named `my_NACL` with the following rules:

#### 🔽 Inbound Rules

| Rule # | Type             | Protocol | Port Range | Source      | Allow/Deny |
|--------|------------------|----------|------------|-------------|------------|
| 100    | SSH              | TCP      | 22         | 0.0.0.0/0   | ALLOW      |
| 200    | All ICMP - IPv4  | ICMP     | ALL        | 0.0.0.0/0   | ALLOW      |

#### 🔼 Outbound Rules

| Rule # | Type               | Protocol | Port Range     | Destination | Allow/Deny |
|--------|--------------------|----------|----------------|-------------|------------|
| 100    | All ICMP - IPv4    | ICMP     | ALL            | 0.0.0.0/0   | ALLOW      |
| 200    | Custom TCP Rule    | TCP      | 1024–65535     | 0.0.0.0/0   | ALLOW      |

- Associated `my_NACL` with both `public_subnet` and `private_subnet`


### ✅ Step 7 – Launching EC2 Instances

Launched **two t2.micro instances** using **Amazon Linux 2 AMI** inside `my_VPC`:

- `public_instance`:
  - Subnet: `public_subnet`
  - Auto-assign Public IP: Enabled

- `private_instance`:
  - Subnet: `private_subnet`
  - Auto-assign Public IP: Disabled
  
🔐 **Note:** I also created and associated a key pair named `myKey.pem` for SSH access.
<img width="1919" height="305" alt="14" src="https://github.com/user-attachments/assets/7c04c0e7-fada-43db-9155-36a040cebb63" />



### ✅ Step 8 – Testing Connectivity

1. Connected to `public_instance` using EC2 Instance Connect  
<img width="1919" height="824" alt="8" src="https://github.com/user-attachments/assets/f0bb8552-9216-482f-a0aa-d08a42701abf" />

2. From the public instance, tested:
   - **Connectivity to `private_instance`** ( `ping 10.0.2.128 -c 5`)
     
<img width="1129" height="302" alt="image" src="https://github.com/user-attachments/assets/7bc6a69b-ce68-4ded-a4f8-0abd868198ee" />
   - **Connectivity to the internet** ( `ping google.com -c 5`)

<img width="806" height="192" alt="10" src="https://github.com/user-attachments/assets/9cdbadc8-870f-4062-b9ba-5ee134a24899" />

---

## ✍️ Author

Made with 💻 by **Nidhal Labri**  
🔗 [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)
