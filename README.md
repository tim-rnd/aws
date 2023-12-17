# n-tier app demo
CloudFormation n-Tier Application demo

# Prerequisites
create a EC2 Key for ssh instance connection EC2->Key pair place it in KeyName field

# Test stacks
1. dbcluster-mysql -> Test DBCluster creation
2. n-tier-app -> Full stack of n-tier app

# Parameters

InstanceType:
Instance Types: https://awscli.amazonaws.com/v2/documentation/api/2.0.33/reference/ec2/describe-instance-types.html


 aws ec2 describe-instance-types --query "InstanceTypes[].InstanceType"
> instanceTypes.txt


"Type": "AWS::RDS::DBClusterParameterGroup",
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Reference.ParameterGroups.html 
family: -> $aws rds describe-db-engine-versions --query "DBEngineVersions[].DBParameterGroupFamily"
Select aurora-mysql8.0 

time_zone: => https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MySQL.Concepts.LocalTimeZone.html 
select US/Pacific

for Cluster Engines:
Valid Values:
 aurora-mysql
 aurora-postgresql
 mysql
 postgres

AMI Images:
aws ec2 describe-images --query "Images[].{Architecture:Architecture,ImageId:ImageId,Platform:PlatformDetails,Name:Name}"
Amazon Linux 2023:=>
    {
        "Architecture": "x86_64",
        "ImageId": "ami-0c00eacddaea828c6",
        "Platform": "Linux/UNIX",
        "Name": "al2023-ami-2023.3.20231211.4-kernel-6.1-x86_64"
    },


for DB instance:
1. use the console, to see the list of available instances per region
2. CLI<-- $aws rds describe-db-instances --query "DBInstances[].DBInstanceClass"


What it does: (approximately 15m)
1. create Networking infrastructure 
   1 VPC , 4 Private subnets, 2 Public Subnets, 1 IGW, 2 NATs 
2. Setup Security
   1 Role for SSM Profile, 1 Launch Template, SSGs for App Server, ALB, and DB Servers
3. Launch Resources
   1 ASG (2 min), 1 ALB, 1 Aurora MySQL Cluster (2 DB Instances multi zones)


# CloudFormation CLI
aws cloudformation create-stack --stack-name N-TIER-APP-021 \
--template-body file:///home/aws/Projects/cloudFormation/n-tier-app/n-tier-app.json \
--capabilities CAPABILITY_NAMED_IAM