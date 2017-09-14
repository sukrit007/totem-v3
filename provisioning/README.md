# Provisioning

## Requirements

- [AWS Account](https://aws.amazon.com/) with [VPC](https://aws.amazon.com/vpc/)
- [VPC Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html) with access to internet. 
- [AWS CLI](https://aws.amazon.com/cli/)
- [SSH Key Pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) for accessing hosts. For security reasons ssh key pair is not created 
  as part of provisioning

## Creating Global Resources

Totem ECS cluster requires certain components to be created for all clusters.
These are created automatically by spinning up cloudformation stack: [totem-environment](./totem-environment.yml).

```bash
aws cloudformation deploy \
  --region=<aws-region> \
  --template-file=./totem-environment.yml \
  --stack-name=totem-environment \
  --capabilities=CAPABILITY_NAMED_IAM
``` 
*Note:* 
- The environment stack requires IAM capabilities to create instance profiles and service roles.
- If the specified stack name already exists, the current stack will be updated.

## Bring up cluster

*Note*: This step assumes that [global resources](#creating-global-resources) have already been created using environment stack. 

```bash
aws cloudformation deploy \
  --region=<aws-region> \
  --template-file=./totem-cluster.yml \
  --stack-name=totem-cluster \
  --parameter-overrides \
    "ECSSubnetID=<comma-separated-subnet-ids>" \
    "KeyName=<ssh-key-pair-name>" \
    "EcsSecurityGroups=<comma-separated-security-group-ids>"
``` 

### Override Parameters
Here is the list of complete parameters that can be overridden

Parameter Name       |Required          |Default Value         |Example Value                                |Description
---------------------|------------------|----------------------|---------------------------------------------|---------------------------------
ECSSubnetID          |Yes               |                      |subnet-fasb4e9e,subnet-ad66abf4              |Comma separated private VPC subnets
KeyName              |Yes               |                      |totem-development                            |SSH Key Pair for ECS hosts                                                    
EcsSecurityGroups    |Yes               |                      |sg-a7fea2ba,sg-a6aeb3ea                      |Comma separated security groups for ECS hosts
DesiredCapacity      |No                |1                     |2                                            |Number of instances to launch in your ECS cluster
MaxSize              |No                |1                     |3                                            |Maximum number of instances that can be launched in your ECS cluster
InstanceType         |No                |t2.micro              |r3.large                                     |EC2 Instance Size
EnvironmentStack     |No                |totem-environment     |totem-environment-production                 |Name of totem environment stack for cross stack reference
StackVersion         |No                |1.0.0                 |1.0.1                                        |Version identifier for current stack
Environment          |No                |development           |production                                   |Environment for the current stack (for tagging)
 
## Tear down cluster
*Note*:
- This step assumes that there are no services being currently deployed to current cluster.

```bash
aws cloudformation delete-stack \
  --region=<aws-region> \
  --stack-name=totem-cluster
```