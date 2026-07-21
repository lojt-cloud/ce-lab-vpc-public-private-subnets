# Connectivity Validation Test

## Overview

This document contains the validation steps performed after creating the AWS VPC network architecture.

The purpose of these tests was to confirm:

- VPC availability
- Internet Gateway attachment
- Public subnet configuration
- Private subnet isolation
- Route table configuration

AWS Region:
us-east-1


---

# Test 1 - Verify VPC Creation

## Objective

Confirm that the VPC was created successfully with the correct CIDR block.

## Command

```bash
aws ec2 describe-vpcs \
--vpc-ids vpc-04ab384385311bf3a

Expected Result

The VPC should show:

State: available
CIDR: 10.0.0.0/16
Name: bootcamp-vpc
Result

PASS ✅

Verified:

Parameter	Value
VPC ID	vpc-04ab384385311bf3a
CIDR Block	10.0.0.0/16
State	available
Name	bootcamp-vpc
Test 2 - Verify Internet Gateway Attachment
Objective

Confirm that the Internet Gateway is attached to the correct VPC.

Command
aws ec2 describe-internet-gateways \
--filters Name=attachment.vpc-id,Values=vpc-04ab384385311bf3a
Expected Result

The Internet Gateway should show:

Attached to bootcamp-vpc
State: available
Result

PASS ✅

Verified:

Parameter	Value
Internet Gateway ID	igw-079f759c6bc339c37
Name	bootcamp-igw
Attached VPC	vpc-04ab384385311bf3a
Test 3 - Verify Subnet Configuration
Objective

Confirm that all required subnets were created across two Availability Zones.

Expected Architecture
Subnet Type	Availability Zone	CIDR
Public	us-east-1a	10.0.1.0/24
Public	us-east-1b	10.0.2.0/24
Private	us-east-1a	10.0.11.0/24
Private	us-east-1b	10.0.12.0/24
Result

PASS ✅

Verified:

2 public subnets created
2 private subnets created
Multi-AZ deployment configured
Test 4 - Verify Public Subnet Internet Configuration
Objective

Confirm that public subnets are configured to receive public IP addresses.

Public Subnets Tested
public-subnet-1a
public-subnet-1b
Configuration

Public IP mapping enabled:

aws ec2 modify-subnet-attribute \
--subnet-id <subnet-id> \
--map-public-ip-on-launch
Expected Result

Instances launched in public subnets automatically receive public IPv4 addresses.

Result

PASS ✅

Public subnet configuration verified.

Test 5 - Verify Public Route Table
Objective

Confirm that public subnets have internet access through the Internet Gateway.

Required Route
0.0.0.0/0 → Internet Gateway
Verification Command
aws ec2 describe-route-tables \
--filters Name=vpc-id,Values=vpc-04ab384385311bf3a
Expected Result

Public route table should contain:

Destination	Target
10.0.0.0/16	local
0.0.0.0/0	igw-079f759c6bc339c37
Result

PASS ✅

Public routing configuration verified.

Test 6 - Verify Private Subnet Isolation
Objective

Confirm that private subnets do not have direct internet access.

Private Subnets Tested
private-subnet-1a
private-subnet-1b
Expected Route Configuration

Private route table should contain only:

Destination	Target
10.0.0.0/16	local

No Internet Gateway route should exist.

Result

PASS ✅

Private subnets remain isolated from direct internet access.

Test Summary
Test	Result
VPC creation	PASS ✅
Internet Gateway attachment	PASS ✅
Subnet creation	PASS ✅
Public subnet configuration	PASS ✅
Public routing configuration	PASS ✅
Private subnet isolation	PASS ✅
Additional EC2 Connectivity Testing
Status

Not performed.

No EC2 instances were deployed as part of this lab implementation.

Future testing could include:

Launching an EC2 instance in a public subnet
Verifying internet connectivity using ping/curl
Launching an EC2 instance in a private subnet
Confirming that it cannot directly access the internet
Conclusion

The AWS VPC networking architecture was successfully validated.

The final configuration provides:

Public subnet access through an Internet Gateway
Private subnet isolation
Multi-AZ subnet distribution
Proper network segmentation