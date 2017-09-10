# Provisioning

## Requirements

- [AWS Account](https://aws.amazon.com/) with [VPC](https://aws.amazon.com/vpc/)
- [VPC Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html) with access to internet
- [AWS CLI](https://aws.amazon.com/cli/)

It is recommended to have at-least 2 IAM user profiles for provisioning.
- totem-iam (With IAM capabilities to create users and roles)
- totem (TBD: Determine permissions)

You can configure these 2 profiles for aws cli using:
```bash
aws configure --profile=totem
aws configure --profile=totem-iam
```

## Creating Global Resources

Totem ECS cluster requires certain components to be created for all clusters.
These can be created automatically by spinning up cloudformation stack: [ecs-environment](./ecs-environment.yml).

*Note:* The environment stack requires IAM capabilities

```bash
aws --profile=totem-iam cloudformation deploy --template-file=./ecs-environment.yml --stack-name=totem-environment --capabilities=CAPABILITY_NAMED_IAM
``` 

## Bring up cluster

## Tearing down cluster