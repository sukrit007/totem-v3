# Provisioning

## Requirements

- [AWS Account](https://aws.amazon.com/) with [VPC](https://aws.amazon.com/vpc/)
- [VPC Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html) with access to internet. 
- [AWS CLI](https://aws.amazon.com/cli/)
- [SSH Key Pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) for accessing hosts. For security reasons ssh key pair is not created 
  as part of provisioning

## Creating Global Resources

Totem ECS cluster requires certain components to be created for all clusters.
These are created automatically by spinning up cloudformation stack: [totem-global](./totem-global.yml).

```bash
aws cloudformation deploy \
  --region=<aws-region> \
  --template-file=./totem-global.yml \
  --stack-name=totem-global \
  --capabilities=CAPABILITY_NAMED_IAM
``` 
*Note:* 
- The global stack requires IAM capabilities to create instance profiles and service roles.
- If the specified stack name already exists, the current stack will be updated.

## Creating Environment Stack

Environment stack is used to create environmental resources for the cluster that requires IAM capabilities like
- instance role
- service role

These environment components can automatically by spinning up cloudformation stack: [totem-environment](./totem-environment.yml).

```bash
set -o pipefail
PROFILE=[AWS_CLI_PROFILE]
TOTEM_BUCKET="$(aws --profile=$PROFILE cloudformation describe-stack-resource \
  --logical-resource-id=TotemBucket \
  --stack-name=totem-global \
  --output text | tail -1 | awk '{print $1}')" &&

OUTPUT_TEMPLATE="$TOTEM_BUCKET/cloudformation/totem-environment.yml" && 

aws --profile=$PROFILE s3 cp ./provisioning/totem-environment.yml s3://$OUTPUT_TEMPLATE &&

aws --profile=$PROFILE cloudformation create-stack \
  --template-url=https://s3.amazonaws.com/$OUTPUT_TEMPLATE \
  --stack-name=totem-environment \
  --capabilities=CAPABILITY_NAMED_IAM \
  --tags \
    "Key=app,Value=totem-v3" \
    "Key=env,Value=development" \
    "Key=client,Value=totem" \
    "Key=stacktype,Value=totem-environment"
```
where:
- **AWS_CLI_PROFILE**: [AWS CLI Profile](http://docs.aws.amazon.com/cli/latest/userguide/cli-multiple-profiles.html)

*Note:* 
- The environment stack requires IAM capabilities to create instance profiles and service roles.
- If the specified stack name already exists, the current stack will be updated.
- This step assumes that [global resources](#creating-global-resources) have already been created using totem global stack.  

## Bring up cluster

*Note*: This step assumes that 
- [global resources](#creating-global-resources) have already been created using global stack. 
- [Environment Stack](#creating-environment-stack) have already been created using environment stack. 

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
