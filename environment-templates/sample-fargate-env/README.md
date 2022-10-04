## Description

This template creates a VPC with two public and private subnets across two availability zones. The VPC includes an Internet Gateway and a managed NAT Gateway in each public subnet as well as VPC Route Tables that allow for communication between the public and private subnets. 

It also deploys an ECS cluster to group your fargate tasks and services. An ECS task execution role is created to allow tasks to pull container images from an ECR private repository, and send container logs to CloudWatch Logs. In order to support Service Discovery, we also create a private namespace based on DNS, which is visible only inside the VPC.

The environment supports asynchronous service-to-service communication using a publish/subscribe model through a shared Amazon SNS topic. In contrast to synchronous communication (i.e. HTTP API), with asynchronous communication, we can avoid blocking the sender to wait for a response as well as decoupling producer from consumer. Multiple services can broadcast events to the SNS topic. All components that subscribe to the topic will receive the message, and can each do something different with the message in parallel. 

## Architecture

![fargate-env](../../images/fargate-env.png)

## Parameters

1. vpc_cidr: The CIDR range for your VPC
2. public_subnet_one_cidr: The CIDR range for public subnet one
3. public_subnet_two_cidr: The CIDR range for public subnet two
4. private_subnet_one_cidr: The CIDR range for private subnet one
5. private_subnet_two_cidr: The CIDR range for private subnet two
6. ecs_cluster_container_insights: Enable or disable ECS Cluster Container Insights.

## Observability

This environment uses the AWS Distro for OpenTelemetry for container observability. 
The vanilla ADOT Collector container image from https://gallery.ecr.aws/aws-observability/aws-otel-collector should be deployed as a sidecar in the service templates.
A custom built container image is not necessary as the environment specific configuration is injected through the contents of an SSM Paramater Store Parameter that at the moment must be created manually until automation is in place. 
The parameter value must be manually populated in every environment with the configuration of choice like [prometheus-ecs-xray-config-sample.yaml](prometheus-ecs-xray-config-sample.yaml). Remember to change the values for "awsprometheusremotewrite" and "region" accordingly.
For more information see:
 - https://aws-otel.github.io/docs/getting-started/prometheus-remote-write-exporter/ecs
 - https://aws-otel.github.io/docs/setup/ecs/config-through-ssm

## License

This library is licensed under the MIT-0 License. See the [LICENSE](../../LICENSE) file.

