#### Genesis-KMA-clouds
### Hometask


### aws-cli installation:
# [Reference](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)


### Usage example:

## 1. Create VPC
# [Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html)
```
aws create-vpc --cidr-block 10.10.0.0/18 \ # create vpc with 16384 grey IP adresses
    --no-amazon-provided-ipv6-cidr-block \ # create only IPv4 
    --tag-specifications Tags=[{Key=Name,Value=kma-genesis},{Key=Lesson,Value=public-clouds}] \ # add some tags, that can be used in future
    --query Vpc.VpcId \ # output VPC ID
    --output text # get value in plain text
```
```
aws ec2 describe-vpcs | grep "VpcId" # get VPC ID
```


## 2. Create subnets, each dedicated to availability zone within region
# [Reference](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnets-commands-example.html)
```
$VPC-ID=<my-vpc-id>; aws ec2 create-subnet --vpc-id="$VPC-ID" --cidr-block 10.10.1.0/24
$VPC-ID=<my-vpc-id>; aws ec2 create-subnet --vpc-id="$VPC-ID" --cidr-block 10.10.2.0/24
$VPC-ID=<my-vpc-id>; aws ec2 create-subnet --vpc-id="$VPC-ID" --cidr-block 10.10.3.0/24
```


# 3. Create Internet Gateway and attach it to vpc
# [Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-internet-gateway.html)
```
aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
IG-ID=<my-ig-id>
```


## 4. Attach IG to VPC
# [Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-internet-gateway.html)
```
aws ec2 attach-internet-gateway --internet-gateway-id $IG-ID --vpc-id $VPC-ID
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
