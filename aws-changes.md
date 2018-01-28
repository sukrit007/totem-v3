# About
This page keeps a track on services changes / announcements for AWS and how it impacts the design.

# Announcement List
Here is a list of all AWS announcements that will be relevant to totem-v3 architecture and development: 

- https://aws.amazon.com/about-aws/whats-new/2017/12/amazon-ecs-adds-elb-health-check-grace-period/
- https://aws.amazon.com/about-aws/whats-new/2017/12/circuit-breaking-logic-for-the-amazon-ecs-service-scheduler/
- https://aws.amazon.com/about-aws/whats-new/2017/12/amazon-cloudwatch-introduces-a-new-cloudwatch-agent-with-aws-systems-manager-integration-for-unified-metrics-and-logs-collection/
- https://aws.amazon.com/about-aws/whats-new/2017/12/amazon-ecs-supports-memory-and-cpu-limits-at-the-task-level/
- https://aws.amazon.com/about-aws/whats-new/2017/12/aws-codepipeline-adds-support-for-amazon-ecs-and-aws-fargate/
- https://aws.amazon.com/about-aws/whats-new/2017/12/amazon-compute-service-level-agreement-extended-to-amazon-ecs-and-aws-fargate/
- https://aws.amazon.com/about-aws/whats-new/2017/12/amazon-route-53-releases-auto-naming-api-name-service-management/
- https://aws.amazon.com/about-aws/whats-new/2017/11/amazon-api-gateway-supports-endpoint-integrations-with-private-vpcs/
- https://aws.amazon.com/about-aws/whats-new/2017/11/introducing-amazon-elastic-container-service-for-kubernetes/
- https://aws.amazon.com/about-aws/whats-new/2017/11/sign-up-for-the-preview-of-amazon-aurora-serverless/
- https://aws.amazon.com/about-aws/whats-new/2017/11/amazon-ecs-cli-version-1-1-0-adds-aws-fargate-support/
- https://aws.amazon.com/about-aws/whats-new/2017/11/introducing-aws-fargate-a-technology-to-run-containers-without-managing-infrastructure/
- https://aws.amazon.com/about-aws/whats-new/2017/11/aws-cloudformation-supports-parameterizing-configurations-with-stacksets-parameter-overrides-and-ec2-systems-manager-parameter-store/
- https://aws.amazon.com/about-aws/whats-new/2017/11/aws-step-functions-adds-support-for-updating-state-machines/
- https://aws.amazon.com/about-aws/whats-new/2017/11/amazon-ecs-introduces-awsvpc-networking-mode-for-containers-to-support-full-networking-capabilities/
- https://aws.amazon.com/about-aws/whats-new/2017/10/elastic-load-balancing-application-load-balancers-now-support-multiple-ssl-certificates-and-smart-certificate-selection-using-server-name-indication-sni/
- https://aws.amazon.com/about-aws/whats-new/2017/10/introducing-lifecycle-policies-for-amazon-ec2-container-registry/
- https://aws.amazon.com/about-aws/whats-new/2017/09/connect-your-git-repository-to-amazon-s3-and-aws-services-using-webhooks-and-new-quick-start/
- https://aws.amazon.com/blogs/aws/new-network-load-balancer-effortless-scaling-to-millions-of-requests-per-second/
- https://aws.amazon.com/about-aws/whats-new/2017/04/elastic-load-balancing-adds-support-for-host-based-routing-and-increased-rules-on-its-application-load-balancer/

# Limitations

## SAM and Cloudformation
- Does not support YAML anchors. (Requires duplication for repeated properties)
- If cloudformation deploy fails for first time, it can not be updated and needs to be deleted.

## Lamda Functions
- Git web hook times out at times if it takes too long for lambda functions to warm up.
