vpc config   

here is the complete design of the VPC .

used commands:

VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=bootcamp-vpc}]'



IGW
aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=bootcamp-igw}]'

connectiong IGW with VPC
aws ec2 attach-internet-gateway --vpc-id vpc-04ab384385311bf3a --internet-gateway-id igw-079f759c6bc339c3
7


VPC id: vpc-04ab384385311bf3a
IGW id: igw-079f759c6bc339c3

subnet (x4)

## 1st public subnet in us-east-1a:

aws ec2 create-subnet \
    --vpc-id vpc-04ab384385311bf3a \
    --cidr-block 10.0.1.0/24 \
    --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=public-subnet-1a}]'

subnet ID: subnet-0d06046ce2cb9d9e2

enabled automatic public IP:

aws ec2 modify-subnet-attribute \
    --subnet-id subnet-0d06046ce2cb9d9e2 \
    --map-public-ip-on-launch


## 1st private subnet in us-east-1a:

aws ec2 create-subnet \
    --vpc-id vpc-04ab384385311bf3a \
    --cidr-block 10.0.11.0/24 \
    --availability-zone us-east-1a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=private-subnet-1a}]'

subnet ID:  subnet-09481b3a06361ac9d

## 2nd public subnet in us-east-1b:

aws ec2 create-subnet \
    --vpc-id vpc-04ab384385311bf3a \
    --cidr-block 10.0.2.0/24 \
    --availability-zone us-east-1b \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=public-subnet-1b}]'

subnet ID: subnet-0d6181a3ff5b0c9b4

enabled public IPs:

aws ec2 modify-subnet-attribute \
    --subnet-id subnet-0d6181a3ff5b0c9b4 \
    --map-public-ip-on-launch


## 2nd private subnet in us-east-1b:

aws ec2 create-subnet \
    --vpc-id vpc-04ab384385311bf3a \
    --cidr-block 10.0.12.0/24 \
    --availability-zone us-east-1b \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=private-subnet-1b}]'

private subnet IP: subnet-0cf3fc08a4c68a839



Route table verification: 

aws ec2 describe-route-tables \
    --filters Name=vpc-id,Values=vpc-04ab384385311bf3a \
    --output text
ROUTETABLES     743631836010    rtb-083f34e00c0a1c3e3   vpc-04ab384385311bf3a
ASSOCIATIONS    True    rtbassoc-0bf70a0c35d70f9a7      rtb-083f34e00c0a1c3e3
ASSOCIATIONSTATE        associated
ROUTES  10.0.0.0/16     local   CreateRouteTable        active

IGW verification: 

aws ec2 describe-internet-gateways \
    --filters Name=attachment.vpc-id,Values=vpc-04ab384385311bf3a
{
    "InternetGateways": [
        {
            "Attachments": [
                {
                    "State": "available",
                    "VpcId": "vpc-04ab384385311bf3a"
                }
            ],
            "InternetGatewayId": "igw-079f759c6bc339c37",
            "OwnerId": "743631836010",
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "bootcamp-igw"
                }
            ]
        }
    ]
}

VPC verification: 

aws ec2 describe-vpcs \
    --vpc-ids vpc-04ab384385311bf3a
{
    "Vpcs": [
        {
            "OwnerId": "743631836010",
            "InstanceTenancy": "default",
            "CidrBlockAssociationSet": [
                {
                    "AssociationId": "vpc-cidr-assoc-0966f6c678ad5df43",
                    "CidrBlock": "10.0.0.0/16",
                    "CidrBlockState": {
                        "State": "associated"
                    }
                }
            ],
            "IsDefault": false,
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "bootcamp-vpc"
                }
            ],
            "BlockPublicAccessStates": {
                "InternetGatewayBlockMode": "off"
            },
            "VpcId": "vpc-04ab384385311bf3a",
            "State": "available",
            "CidrBlock": "10.0.0.0/16",
            "DhcpOptionsId": "dopt-05ffd4bbf0ecb5b27"
        }
    ]
}





