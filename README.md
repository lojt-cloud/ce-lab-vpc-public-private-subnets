# AWS VPC Networking Lab

## Overview

This repository documents the design, deployment, and configuration of a custom AWS VPC network architecture using AWS CLI.

The goal of this lab was to create a structured AWS networking environment with public and private subnets, an Internet Gateway, and supporting route table configuration.

The infrastructure was created in the AWS `us-east-1` region.

---

## Architecture

The VPC architecture consists of:

- 1 Custom VPC
- 1 Internet Gateway
- 2 Public Subnets
- 2 Private Subnets
- Route Tables
- Network validation documentation


### Network Design

VPC: 10.0.0.0/16

├── Public Subnet 1
│ └── us-east-1a
│ └── 10.0.1.0/24
│
├── Public Subnet 2
│ └── us-east-1b
│ └── 10.0.2.0/24
│
├── Private Subnet 1
│ └── us-east-1a
│ └── 10.0.11.0/24
│
└── Private Subnet 2
└── us-east-1b
└── 10.0.12.0/24


---

## AWS Resources Created

### VPC

| Resource | Value |
|---|---|
| Name | bootcamp-vpc |
| VPC ID | vpc-04ab384385311bf3a |
| CIDR Block | 10.0.0.0/16 |
| Region | us-east-1 |


### Internet Gateway

| Resource | Value |
|---|---|
| Name | bootcamp-igw |
| Internet Gateway ID | igw-079f759c6bc339c37 |
| Attached VPC | bootcamp-vpc |

---

## Repository Structure
.
├── README.md
├── SOLUTION.md
├── LAB-INSTRUCTIONS.md
│
├── network-config
│ ├── vpc-config.md
│ ├── subnet-config.md
│ └── route-tables.md
│
├── screenshots
│ └── AWS architecture screenshots
│
└── validation
└── connectivity-test.md


---

## Documentation

### Lab Instructions

`LAB-INSTRUCTIONS.md`

Contains the original requirements and objectives for the networking lab.

---

### Solution

`SOLUTION.md`

Contains the implementation process, including:

- AWS CLI commands used
- Deployment steps
- Resource creation process
- Configuration decisions

---

### Network Configuration

The `network-config` directory contains the final state of the AWS networking components:

- `vpc-config.md`  
  VPC and Internet Gateway details

- `subnet-config.md`  
  Public and private subnet configuration

- `route-tables.md`  
  Route table configuration and associations

---

### Validation

The `validation` directory contains verification results for:

- VPC creation
- Internet Gateway attachment
- Route table configuration
- Connectivity testing

---

## Deployment Method

The infrastructure was created using AWS CLI commands and checked via console and commands.