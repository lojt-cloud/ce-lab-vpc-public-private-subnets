# Solution - AWS VPC Networking Lab

## Overview

This document describes the implementation process used to create the AWS VPC network architecture.

The deployment was completed using AWS CLI commands.

The final architecture consists of:

- 1 custom VPC
- 1 Internet Gateway
- 2 public subnets
- 2 private subnets
- Public and private route table configuration

AWS Region:
us-east-1


---

# Step 1 - Create VPC

A custom VPC was created using the required CIDR block:
10.0.0.0/16


Command used:

```bash
aws ec2 create-vpc \
--cidr-block 10.0.0.0/16 \
--tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=bootcamp-vpc}]'

Created resource:

| Resource | Value                 |
| -------- | --------------------- |
| VPC Name | bootcamp-vpc          |
| VPC ID   | vpc-04ab384385311bf3a |
| CIDR     | 10.0.0.0/16           |

Step 2 - Create and Attach Internet Gateway

An Internet Gateway was created to provide internet access for public subnets.

Command used:

aws ec2 create-internet-gateway \
--tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=bootcamp-igw}]'

The Internet Gateway was attached to the VPC:

aws ec2 attach-internet-gateway \
--vpc-id vpc-04ab384385311bf3a \
--internet-gateway-id igw-079f759c6bc339c37

| Resource     | Value                 |
| ------------ | --------------------- |
| IGW Name     | bootcamp-igw          |
| IGW ID       | igw-079f759c6bc339c37 |
| Attached VPC | bootcamp-vpc          |

Step 3 - Create Subnets

Four subnets were created across two Availability Zones.

Public Subnets
Public Subnet 1

Availability Zone:
us-east-1a

CIDR:
10.0.1.0/24
Command:
aws ec2 create-subnet \
--vpc-id vpc-04ab384385311bf3a \
--cidr-block 10.0.1.0/24 \
--availability-zone us-east-1a \
--tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=public-subnet-1a}]'

subnet ID: 
subnet-0d06046ce2cb9d9e2

Public IP assignment was enabled: 
aws ec2 modify-subnet-attribute \
--subnet-id subnet-0d06046ce2cb9d9e2 \
--map-public-ip-on-launch

Public subnet 2
avaliability zone: 
us-east-1b

CIDR:
10.0.2.0/24

Subnet ID: 
subnet-0d6181a3ff5b0c9b4
public Ip assignment was enabled.

Private subnets
private subnet 1

availability zone: 
us-east-1a

CIDR: 
10.0.11.0/24

subnet ID: 
subnet-09481b3a06361ac9d

private subnet 2

Availability Zone:
us-east-1b

CIDR:
10.0.12.0/24

Subnet ID:
subnet-0cf3fc08a4c68a839

Step 4 - Route Table Configuration

Route tables were configured to separate public and private network traffic.

Public Routing

The public route table was configured with:

0.0.0.0/0 → Internet Gateway

This allows resources inside public subnets to access the internet.

Public subnets:

public-subnet-1a
public-subnet-1b
Private Routing

The private route table was configured with only the default local route:

10.0.0.0/16 → local

This keeps private subnets isolated from direct internet access.

Private subnets:

private-subnet-1a
private-subnet-1b
Step 5 - Validation

The deployed resources were verified using AWS CLI commands.

VPC Verification

Command:

aws ec2 describe-vpcs \
--vpc-ids vpc-04ab384385311bf3a

Confirmed:

VPC exists
CIDR is correct
Name tag is correct
VPC state is available
Internet Gateway Verification

Command:

aws ec2 describe-internet-gateways \
--filters Name=attachment.vpc-id,Values=vpc-04ab384385311bf3a

Confirmed:

Internet Gateway exists
IGW is attached to the correct VPC
Route Table Verification

Command:

aws ec2 describe-route-tables \
--filters Name=vpc-id,Values=vpc-04ab384385311bf3a

Confirmed:

Route tables exist
Associations are configured
Challenges and Solutions
Challenge: Tracking AWS Resource IDs

AWS CLI commands return unique IDs for every resource created.

Solution:

Resource IDs were documented immediately after creation and stored in the network configuration files.

Challenge: Separating Public and Private Networking

Public and private subnets require different routing behaviour.

Solution:

Separate route tables were created:

Public route table → Internet Gateway access
Private route table → Local VPC communication only
Final Result

The AWS VPC architecture was successfully created with:

✅ Custom VPC
✅ Internet Gateway
✅ Two public subnets
✅ Two private subnets
✅ Multi-AZ subnet deployment
✅ Public/private network segmentation
✅ Route table configuration