#### Genesis-KMA-clouds
### Hometask


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
```
OUTPUT 
igw-00de7ec03e8ab8d2b
```
## 4. Attach IG to VPC
```
aws ec2 attach-internet-gateway --internet-gateway-id igw-00de7ec03e8ab8d2b --vpc-id vpc-01f75f4c63fde480e
```
## 5. AWS Security group
```
aws ec2 describe-security-groups --filters "Name=vpc-id,Values=vpc-01f75f4c63fde480e"
```
```
OUTPUT
{
    "SecurityGroups": [
        {
            "Description": "default VPC security group",
            "GroupName": "default",
            "IpPermissions": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": [
                        {
                            "GroupId": "sg-0b39f67dfc85d58a4",
                            "UserId": "692731609171"
                        }
                    ]
                }
            ],
            "OwnerId": "692731609171",
            "GroupId": "sg-0b39f67dfc85d58a4",
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "Ipv6Ranges": [],
                    "PrefixListIds": [],
                    "UserIdGroupPairs": []
                }
            ],
            "VpcId": "vpc-01f75f4c63fde480e"
        }
    ]
}
```
```
aws ec2 create-security-group \
    --group-name KmaGenesisSecurityGroup \
    --description "Sone description" \
    --vpc-id vpc-01f75f4c63fde480e
   ```
   
   ``` 
    OUTPUT
  {
    "GroupId": "sg-04c6876dc6d4e51e4"
  }
```

## 6. Add to your EC2 instance Security groups, that allows connection to TCP ports 22 (SSH), 80 (HTTP), 443 (HTTPS)
```
aws ec2 authorize-security-group-ingress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 22 --source-group sg-0b39f67dfc85d58a4
aws ec2 authorize-security-group-ingress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 80 --source-group sg-0b39f67dfc85d58a4
aws ec2 authorize-security-group-ingress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 443--source-group sg-0b39f67dfc85d58a4
aws ec2 authorize-security-group-egress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 80 --source-group sg-0b39f67dfc85d58a4
aws ec2 authorize-security-group-egress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 22 --source-group sg-0b39f67dfc85d58a4
aws ec2 authorize-security-group-egress --group-id sg-04c6876dc6d4e51e4 --protocol tcp --port 443--source-group sg-0b39f67dfc85d58a4
```
  6. Put Application Load Balancer (ALB) as a proxy to your ASG
## 7 EC2 instance within ASG based on latest AMI Amazon Linux 2 with 15GiB attached EBS (Elastic block storage)
# 7.1 GET AMI
```
aws ssm get-parameters --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
```
```
OUTPUT
{
    "Parameters": [
        {
            "Name": "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2",
            "Type": "String",
            "Value": "ami-074cce78125f09d61",
            "Version": 51,
            "LastModifiedDate": "2021-10-06T23:50:43.294000+00:00",
            "ARN": "arn:aws:ssm:us-east-2::parameter/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2",
            "DataType": "text"
        }
    ],
    "InvalidParameters": []
}
```
# 7.2 Run Instance
```
aws ec2 run-instances --image-id ami-074cce78125f09d61 --instance-type t2.micro  --security-group-ids sg-0b39f67dfc85d58a4 --subnet-id subnet-012c0c99eab51b865
```
# 7.3 Volume
```
aws ec2 create-volume     --volume-type gp2  --region eu-central-1 --availability-zone eu-central-1b --size 80
```

```
OUTPUT
{
    "AvailabilityZone": "eu-central-1b",
    "CreateTime": "2021-10-30T12:23:14+00:00",
    "Encrypted": false,
    "Size": 80,
    "SnapshotId": "",
    "State": "creating",
    "VolumeId": "vol-063885461986df8d0",
    "Iops": 240,
    "Tags": [],
    "VolumeType": "gp2",
    "MultiAttachEnabled": false
}
[cloudshell-user@ip-10-0-47-175 ~]$ 

```
aws ec2 attach-volume --instance-id i-070b9c16e64d946d1 --volume-id vol-063885461986df8d0 --device a
```
# Fix your result as list of commands in .md file in your git repo
# Your results should be reproducable

P. S. After complition, don't forget to destroy all your infrastructure. And good luck!
P. S. S. Don't hesitate to contact me in any case
