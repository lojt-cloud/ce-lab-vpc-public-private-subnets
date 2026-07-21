# Lab M3.01 - Create VPC with Public and Private Subnets

## Activity Information

**Activity Type:** Individual  
**Estimated Time:** 45-60 minutes

---

# Learning Objectives

By completing this lab, you will:

- Create a custom VPC with an appropriate CIDR block
- Create public and private subnets across multiple Availability Zones
- Configure an Internet Gateway for public access
- Set up route tables for public and private subnet traffic
- Validate network connectivity and routing

---

# Task Overview

Build a production-ready AWS VPC network architecture with proper subnet segmentation.

The architecture must include:

- 1 custom VPC
- 2 public subnets
- 2 private subnets
- 2 Availability Zones
- 1 Internet Gateway
- Public and private route tables

---

# Network Requirements

## VPC

CIDR Block:
10.0.0.0/16

---

## Public Subnets

| Availability Zone | CIDR Block |
|---|---|
| us-east-1a | 10.0.1.0/24 |
| us-east-1b | 10.0.2.0/24 |

Requirements:

- Public IP assignment enabled
- Internet access through Internet Gateway

---

## Private Subnets

| Availability Zone | CIDR Block |
|---|---|
| us-east-1a | 10.0.11.0/24 |
| us-east-1b | 10.0.12.0/24 |

Requirements:

- No direct internet access
- Remain isolated using private routing

---

# Required Components

## 1. VPC Creation

Create a VPC with:

- Name: `bootcamp-vpc`
- CIDR: `10.0.0.0/16`
- Default tenancy

---

## 2. Internet Gateway

Create and attach an Internet Gateway:

- Name: `bootcamp-igw`
- Attach it to the created VPC

---

## 3. Subnet Configuration

Create four subnets:

- Two public subnets across two Availability Zones
- Two private subnets across two Availability Zones

Enable automatic public IPv4 assignment for public subnets.

---

## 4. Route Tables

Configure routing:

### Public Route Table

Must contain:

| Destination | Target |
|---|---|
| VPC CIDR | local |
| 0.0.0.0/0 | Internet Gateway |

Associate with:

- Public subnet 1
- Public subnet 2


### Private Route Table

Must contain:

| Destination | Target |
|---|---|
| VPC CIDR | local |

Associate with:

- Private subnet 1
- Private subnet 2

---

# Success Criteria

The completed architecture should meet the following:

- VPC created with `10.0.0.0/16`
- Four subnets created across two Availability Zones
- Public and private subnet separation implemented
- Internet Gateway attached
- Public route table configured with Internet Gateway route
- Private route table contains only local routing
- Public subnets can reach the internet
- Private subnets remain isolated

---

# Submission Requirements

The GitHub repository must contain:

## README.md

Must include:

- Network design explanation
- CIDR planning
- Step-by-step process
- Architecture diagram
- Challenges and solutions
- Reflection answers

---

## Network Configuration

Folder:
network-config/

Required files:
vpc-config.txt
subnet-config.txt
route-tables.txt


These files should document:

- VPC details
- Subnet configurations
- Route table configurations

---

## Screenshots

Folder:
screenshots/


Required evidence:

- VPC Dashboard
- All four subnets
- Internet Gateway attachment
- Public route table
- Private route table
- Resource map view

---

## Validation Tests

Folder:
validation/

Required file:
connectivity-test.md


Should document:

- How connectivity was tested
- Public subnet validation
- Private subnet isolation validation

---

# Grading Criteria

| Category | Points |
|---|---:|
| VPC and subnet configuration | 30 |
| Route table configuration | 25 |
| Internet Gateway setup | 20 |
| Documentation and architecture diagram | 15 |
| Testing and validation | 10 |
| **Total** | **100** |

---

# Reflection Questions

Answer these questions in the README:

1. Why use `10.0.0.0/16` instead of a smaller CIDR block such as `10.0.0.0/24`?

2. What is the difference between public and private subnets?

3. Why deploy resources across multiple Availability Zones?

4. How many usable IP addresses exist in a `/24` subnet in AWS?

5. Can additional subnets be created later without changing the VPC?

---

# Bonus Challenges

Optional improvements:

- Create six subnets across three Availability Zones
- Enable VPC Flow Logs
- Configure custom Network ACL rules
- Document a disaster recovery subnet strategy