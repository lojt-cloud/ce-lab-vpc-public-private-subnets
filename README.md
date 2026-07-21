# Lab M3.01 - Create VPC with Public and Private Subnets

**Repository:** [https://github.com/cloud-engineering-bootcamp/ce-lab-vpc-public-private-subnets](https://github.com/cloud-engineering-bootcamp/ce-lab-vpc-public-private-subnets)

**Activity Type:** Individual  
**Estimated Time:** 45-60 minutes

## Learning Objectives

- [ ] Create a custom VPC with appropriate CIDR block
- [ ] Create public and private subnets across multiple AZs
- [ ] Configure Internet Gateway for public access
- [ ] Set up route tables for public and private subnets
- [ ] Validate network connectivity and routing

## Your Task

Build a production-ready VPC with proper subnet segmentation:
1. Create VPC with 10.0.0.0/16 CIDR block
2. Create 4 subnets across 2 Availability Zones (2 public, 2 private)
3. Attach Internet Gateway to VPC
4. Configure route tables for public/private traffic
5. Document your network architecture

**Success Criteria:** Public subnets can reach internet, private subnets are isolated.

## Quick Start

```bash
# 1. Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=bootcamp-vpc}]'

# 2. Create Internet Gateway
aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=bootcamp-igw}]'

# 3. Attach IGW to VPC
aws ec2 attach-internet-gateway --vpc-id vpc-xxxxx --internet-gateway-id igw-xxxxx

# 4. Create Subnets (repeat for each)
aws ec2 create-subnet --vpc-id vpc-xxxxx --cidr-block 10.0.1.0/24 --availability-zone us-east-1a

# 5. Create and Configure Route Tables
# (See detailed instructions below)
```

## 📤 What to Submit

**Submission Type:** GitHub Repository

Create a **public** GitHub repository named `ce-lab-vpc-networking` containing:

### Required Files

**1. README.md**
- Your network design explanation
- CIDR block planning document
- Step-by-step process you followed
- Architecture diagram (draw.io or similar)
- Challenges and solutions

**2. Network Configuration**
- `vpc-config.txt` - VPC details
- `subnet-config.txt` - All subnet configurations
- `route-tables.txt` - Route table configurations

**3. Screenshots** (in `screenshots/` folder)
- VPC Dashboard showing your VPC
- All 4 subnets
- Internet Gateway attachment
- Public route table with IGW route
- Private route table (local only)
- Resource map view

**4. Validation Tests** (`validation/` folder)
- `connectivity-test.md` - How you tested public/private connectivity
- Test EC2 instance results (optional)

### Repository Structure
```
ce-lab-vpc-networking/
├── README.md
├── architecture-diagram.png
├── network-config/
│   ├── vpc-config.txt
│   ├── subnet-config.txt
│   └── route-tables.txt
├── screenshots/
│   ├── 01-vpc-dashboard.png
│   ├── 02-subnets-list.png
│   ├── 03-igw-attachment.png
│   ├── 04-public-route-table.png
│   ├── 05-private-route-table.png
│   └── 06-resource-map.png
└── validation/
    └── connectivity-test.md
```

## Grading: 100 points

- VPC and subnets configuration: 30pts
- Route tables properly configured: 25pts
- Internet Gateway setup: 20pts
- Documentation and diagram: 15pts
- Testing and validation: 10pts

## Detailed Instructions

### Part 1: Plan Your Network (10 min)

**CIDR Planning:**
```
VPC:              10.0.0.0/16 (65,536 IPs)

Public Subnets:
- us-east-1a:     10.0.1.0/24 (256 IPs)
- us-east-1b:     10.0.2.0/24 (256 IPs)

Private Subnets:
- us-east-1a:     10.0.11.0/24 (256 IPs)
- us-east-1b:     10.0.12.0/24 (256 IPs)

Reserved for future: 10.0.3.0-10.0.10.255, 10.0.13.0-10.0.255.255
```

### Part 2: Create VPC (5 min)

**AWS Console:**
1. Go to VPC Dashboard
2. Create VPC
   - Name: `bootcamp-vpc`
   - IPv4 CIDR: `10.0.0.0/16`
   - Tenancy: Default
3. Enable DNS hostnames (Actions → Edit DNS hostnames)

**AWS CLI:**
```bash
# Create VPC
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=bootcamp-vpc}]' \
  --query 'Vpc.VpcId' --output text)

echo "VPC ID: $VPC_ID"

# Enable DNS hostnames
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnamesaws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnames
```

### Part 3: Create Internet Gateway (5 min)

```bash
# Create IGW
IGW_ID=$(aws ec2 create-internet-gateway \
  --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=bootcamp-igw}]' \
  --query 'InternetGateway.InternetGatewayId' --output text)

echo "IGW ID: $IGW_ID"

# Attach to VPC
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW_ID
```

### Part 4: Create Subnets (15 min)

**Public Subnet 1 (AZ-A):**
```bash
PUBLIC_SUBNET_1=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=public-subnet-1a}]' \
  --query 'Subnet.SubnetId' --output text)

# Enable auto-assign public IP
aws ec2 modify-subnet-attribute --subnet-id $PUBLIC_SUBNET_1 --map-public-ip-on-launch
```

**Public Subnet 2 (AZ-B):**
```bash
PUBLIC_SUBNET_2=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.2.0/24 \
  --availability-zone us-east-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=public-subnet-1b}]' \
  --query 'Subnet.SubnetId' --output text)

aws ec2 modify-subnet-attribute --subnet-id $PUBLIC_SUBNET_2 --map-public-ip-on-launch
```

**Private Subnet 1 (AZ-A):**
```bash
PRIVATE_SUBNET_1=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.11.0/24 \
  --availability-zone us-east-1a \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=private-subnet-1a}]' \
  --query 'Subnet.SubnetId' --output text)
```

**Private Subnet 2 (AZ-B):**
```bash
PRIVATE_SUBNET_2=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.12.0/24 \
  --availability-zone us-east-1b \
  --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=private-subnet-1b}]' \
  --query 'Subnet.SubnetId' --output text)
```

### Part 5: Create Route Tables (15 min)

**Public Route Table:**
```bash
# Create route table
PUBLIC_RT=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=public-rt}]' \
  --query 'RouteTable.RouteTableId' --output text)

# Add route to IGW
aws ec2 create-route --route-table-id $PUBLIC_RT --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW_ID

# Associate with public subnets
aws ec2 associate-route-table --route-table-id $PUBLIC_RT --subnet-id $PUBLIC_SUBNET_1
aws ec2 associate-route-table --route-table-id $PUBLIC_RT --subnet-id $PUBLIC_SUBNET_2
```

**Private Route Table:**
```bash
# Create route table
PRIVATE_RT=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=private-rt}]' \
  --query 'RouteTable.RouteTableId' --output text)

# No IGW route - keeps it private!

# Associate with private subnets
aws ec2 associate-route-table --route-table-id $PRIVATE_RT --subnet-id $PRIVATE_SUBNET_1
aws ec2 associate-route-table --route-table-id $PRIVATE_RT --subnet-id $PRIVATE_SUBNET_2
```

### Part 6: Validate Configuration (10 min)

**Verification Checklist:**

- [ ] VPC created with 10.0.0.0/16 CIDR
- [ ] 4 subnets created (2 public, 2 private) across 2 AZs
- [ ] Internet Gateway attached to VPC
- [ ] Public route table has route to IGW (0.0.0.0/0 → igw-xxxxx)
- [ ] Private route table has only local route (10.0.0.0/16 → local)
- [ ] Public subnets associated with public route table
- [ ] Private subnets associated with private route table
- [ ] Auto-assign public IP enabled for public subnets

**View Route Tables:**
```bash
# Public route table
aws ec2 describe-route-tables --route-table-ids $PUBLIC_RT

# Should show:
# - 10.0.0.0/16 → local
# - 0.0.0.0/0 → igw-xxxxx

# Private route table
aws ec2 describe-route-tables --route-table-ids $PRIVATE_RT

# Should show:
# - 10.0.0.0/16 → local (ONLY)
```

## Reflection Questions

Answer these in your README:

1. **Why use 10.0.0.0/16 instead of 10.0.0.0/24?**
   - Hint: Think about future growth

2. **What's the difference between public and private subnets?**
   - Hint: Route tables!

3. **Why deploy across multiple Availability Zones?**
   - Hint: What if one data center fails?

4. **How many usable IP addresses are in 10.0.1.0/24?**
   - Hint: AWS reserves 5 IPs

5. **Could you add more subnets later without changing the VPC?**
   - Hint: Look at your reserved IP space

## Bonus Challenges

**+5 points each:**
- [ ] Create 6 subnets (3 AZs) instead of 4
- [ ] Create a VPC flow log to capture network traffic
- [ ] Set up a Network ACL with custom rules
- [ ] Document a disaster recovery subnet strategy

## Troubleshooting

**Issue: Can't create subnet**
- Check VPC CIDR doesn't overlap
- Ensure subnet CIDR is within VPC CIDR
- Verify Availability Zone exists in your region

**Issue: Can't attach Internet Gateway**
- Only 1 IGW per VPC allowed
- Ensure IGW isn't already attached to another VPC
- Check VPC ID is correct

**Issue: Route not working**
- Verify IGW is attached to VPC
- Check route table associations
- Ensure subnet is associated with correct route table

## Resources

- [VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [CIDR Calculator](https://www.ipaddressguide.com/cidr)
- [Subnet Calculator](https://www.subnet-calculator.com/)
- [AWS VPC Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-best-practices.html)

---

**Great work on building your first VPC!** 🎉
