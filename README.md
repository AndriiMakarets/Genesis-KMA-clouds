#### Genesis-KMA-clouds
### Hometask


### aws-cli installation:


### Usage example:

## 1. Create VPC
```
aws ec2 create-vpc --cidr-block 10.10.0.0/18 \
                    --no-amazon-provided-ipv6-cidr-block \
                    --query Vpc.VpcId \
                    --output text
```
```
OUTPUT
vpc-01f75f4c63fde480e
```


## 2. Create subnets, each dedicated to availability zone within region
```
 aws ec2 create-subnet --vpc-id="vpc-01f75f4c63fde480e" --cidr-block 10.10.1.0/24
 aws ec2 create-subnet --vpc-id="vpc-01f75f4c63fde480e" --cidr-block 10.10.2.0/24
 aws ec2 create-subnet --vpc-id="vpc-01f75f4c63fde480e" --cidr-block 10.10.3.0/24
```
```
OUTPUT
{
    "Subnet": {
        "AvailabilityZone": "us-east-2a",
        "AvailabilityZoneId": "use2-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.10.1.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-012c0c99eab51b865",
        "VpcId": "vpc-01f75f4c63fde480e",
        "OwnerId": "692731609171",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-east-2:692731609171:subnet/subnet-012c0c99eab51b865"
    }
}
{
    "Subnet": {
        "AvailabilityZone": "us-east-2a",
        "AvailabilityZoneId": "use2-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.10.2.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-05569084d06110663",
        "VpcId": "vpc-01f75f4c63fde480e",
        "OwnerId": "692731609171",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-east-2:692731609171:subnet/subnet-05569084d06110663"
    }
}
{
    "Subnet": {
        "AvailabilityZone": "us-east-2a",
        "AvailabilityZoneId": "use2-az1",
        "AvailableIpAddressCount": 251,
        "CidrBlock": "10.10.3.0/24",
        "DefaultForAz": false,
        "MapPublicIpOnLaunch": false,
        "State": "available",
        "SubnetId": "subnet-0539e8f6d09eb80b1",
        "VpcId": "vpc-01f75f4c63fde480e",
        "OwnerId": "692731609171",
        "AssignIpv6AddressOnCreation": false,
        "Ipv6CidrBlockAssociationSet": [],
        "SubnetArn": "arn:aws:ec2:us-east-2:692731609171:subnet/subnet-0539e8f6d09eb80b1"
    }
}
```

# 3. Create Internet Gateway and attach it to vpc
```
aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
```
OUTPUT 
igw-00de7ec03e8ab8d2b

## 4. Attach IG to VPC
```
aws ec2 attach-internet-gateway --internet-gateway-id igw-00de7ec03e8ab8d2b --vpc-id vpc-01f75f4c63fde480e
```


## Hometask:
# Create:
  1. VPC
  2. 3 subnets within your VPC
  3. AWS Autoscaling group (ASG)
  4. EC2 instance within ASG based on latest AMI Amazon Linux 2 with 15GiB attached EBS (Elastic block storage)
  5. Add to your EC2 instance Security groups, that allows connection to TCP ports 22 (SSH), 80 (HTTP), 443 (HTTPS)
  6. Put Application Load Balancer (ALB) as a proxy to your ASG
# Fix your result as list of commands in .md file in your git repo
# Your results should be reproducable

P. S. After complition, don't forget to destroy all your infrastructure. And good luck!
P. S. S. Don't hesitate to contact me in any case
