# ⚖️ Auto Scaling Web Application on EC2 with Load Balancer

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws)
![EC2](https://img.shields.io/badge/EC2-Compute-blue?logo=amazonec2)
![VPC](https://img.shields.io/badge/VPC-Networking-purple)
![RDS](https://img.shields.io/badge/RDS-Database-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## 📋 Project Overview

This project demonstrates how to build a **highly available, fault-tolerant web application** on AWS that automatically scales based on real-time traffic demand — keeping costs low and performance high.

---

## 🚨 Problem

Web applications face unpredictable traffic. Too little capacity = crashes. Too much capacity = wasted money. Manual scaling is slow and unreliable.

---

## ✅ Solution

Built a fully automated, self-healing web application infrastructure using:

| AWS Service | Purpose |
|-------------|---------|
| **EC2** | Web server instances running the application |
| **VPC** | Isolated network with public and private subnets |
| **ALB** | Application Load Balancer to distribute traffic evenly |
| **Auto Scaling Group** | Automatically add/remove EC2 instances based on demand |
| **RDS** | Managed MySQL database in a private subnet |
| **IAM** | Roles and permissions for secure EC2 access |

---

## 🏆 Result

- ✅ **Auto Scaling** launches new instances during traffic spikes
- ✅ **Load Balancer** distributes traffic with no single point of failure
- ✅ **Multi-AZ deployment** for high availability
- ✅ **Private subnet RDS** for secure database access
- ✅ Cost-efficient — scales down automatically during low traffic

---

## 🏗️ Architecture Diagram

```
Internet
    │
    ▼
Application Load Balancer (ALB)
    │           │
    ▼           ▼
EC2 (AZ-1)  EC2 (AZ-2)     ← Auto Scaling Group
    │           │
    └─────┬─────┘
          │
          ▼
    RDS MySQL (Private Subnet)
          │
    IAM Roles (Security)

VPC
├── Public Subnet  (EC2 instances, ALB)
└── Private Subnet (RDS Database)
```

---

## 🛠️ Step-by-Step Setup

### Step 1: Create VPC and Subnets
```bash
# Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16

# Create public subnet (AZ-1)
aws ec2 create-subnet \
  --vpc-id vpc-xxxxxxxx \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a

# Create private subnet (AZ-2)
aws ec2 create-subnet \
  --vpc-id vpc-xxxxxxxx \
  --cidr-block 10.0.2.0/24 \
  --availability-zone us-east-1b
```

### Step 2: Launch EC2 with User Data
```bash
#!/bin/bash
# User data script to install web server
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname)</h1>" > /var/www/html/index.html
```

### Step 3: Create Application Load Balancer
- Go to **EC2 Console** → Load Balancers → Create ALB
- Set scheme to **Internet-facing**
- Add both **public subnets**
- Create a **Target Group** pointing to your EC2 instances

### Step 4: Configure Auto Scaling Group
```bash
# Create launch template first, then:
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name my-asg \
  --launch-template LaunchTemplateName=my-template \
  --min-size 1 \
  --max-size 4 \
  --desired-capacity 2 \
  --target-group-arns arn:aws:elasticloadbalancing:...
```

### Step 5: Set Scaling Policies
- **Scale Out:** Add instance when CPU > 70% for 2 minutes
- **Scale In:** Remove instance when CPU < 30% for 5 minutes

### Step 6: Launch RDS in Private Subnet
```bash
aws rds create-db-instance \
  --db-instance-identifier my-database \
  --db-instance-class db.t3.micro \
  --engine mysql \
  --master-username admin \
  --master-user-password YourPassword123 \
  --allocated-storage 20 \
  --no-publicly-accessible
```

---

## 📁 Project Files

```
project2-autoscaling-webapp/
├── scripts/
│   ├── userdata.sh          # EC2 bootstrap script
│   ├── create-vpc.sh        # VPC setup commands
│   └── create-asg.sh        # Auto Scaling Group setup
├── policies/
│   └── iam-role-policy.json # EC2 IAM role
└── README.md
```

---

## 💡 Key Learnings

- How VPC subnets separate public and private resources
- How ALB distributes traffic across multiple EC2 instances
- How Auto Scaling responds to CPU metrics automatically
- How RDS in a private subnet stays secure from the internet
- How IAM roles grant EC2 permissions without hardcoding credentials

---

## 🔗 Resources

- [Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)
- [Application Load Balancer Docs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [Amazon RDS Documentation](https://docs.aws.amazon.com/rds/)
- [VPC Documentation](https://docs.aws.amazon.com/vpc/)

---

*Built as part of the AWS re/Start Program* ☁️
