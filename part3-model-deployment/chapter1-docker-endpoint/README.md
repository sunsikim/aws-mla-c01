# ECS(Elastic Container Service) topics

AWS native container orchestration service. No fine grained services like Kubernets control plane or helm chart, but simpler to use because AWS manages task scheduling and container placement.

Other alternative is EKS, a container orchestration service based on AWS managed Kubernetes engine. If you're primarily working within AWS and don't need Kubernetes-specific features, ECS is simpler. However, you should consider using EKS in expense of complexity when:

* you're dealing with Kubernetes workloads 
* need multi-cloud compatibility
* requires advanced storage class like FSx for Lustre

## Infrastructure as Code

CloudFormation or AWS CDK can be useful tool when configuring infrastructure ECS environment.

* **CloudFormation** : combined with *AWS Infrastructure Composer*, user can define infrastructure in JSON or YAML
* **AWS CDK** : wrapper for CloudFormation which compiles Python/Java/TS/.NET code as CloudFormation template using `cdk synth`

## Use cases

Task defined within docker container can be classified as following categories.

### Batch job

User can leverage *AWS Batch* to trigger execution of batch jobs defined as Docker images. This serverless service includes following features:

* only pay for underlying EC2 instances provisioned dynamically
* scheduled using CloudWatch Events and orchestrated by *AWS Step* functions defined by ASL(Amazon State Language) or MWAA(schedulers and workers are AWS Fargate containers, too!)

Don't confuse this with batch job functionality that AWS Glue provides. It is primarily oriented to ETL tasks, which only includes Spark, Scala or Python code whereas AWS Batch is more general in that it executes any computing job and only requires Docker image that defines the job.

### Inference job

In this scenario, keyword to keep in mind would be *load balancing*. This concept is useful when distributing application traffic when exposing API endpoint to user. Note that EFS is good choice for serverless file system used across various containers within ECS(complete match with Fargate).

* **Application Load Balancer** : go-to choice for most use cases
* **Network Load Balancer** : recommended only when high throuput is required, or when pairing with AWS Private link

## Launch Types

To launch Docker containers on AWS means launching ECS Tasks on ECS cluster.

* **Fargate launch type**
    * option to let AWS provision the infrastructure(easiest way to start)
    * user only creates task definitions and AWS runs ECS tasks on behalf
    * to scale, just increase the number of tasks
* **EC2 launch type**
    * option to provision and maintain EC2 based infrastructure by yourself
    * each EC2 instance must run the *ECS Agent* to be registered in ECS cluster
    * AWS takes care of starting/stopping containers within each instance

## IAM Roles

IAM roles required by ECS to run containers on behalf of users.

* **ECS Task Role**
    * task by task AWS role setting
    * ex. task A gets S3 permission and task B gets DynamoDB permission
* **EC2 Instance Profile**
    * used by ECS Agent to make API calls to ECS service when using EC2 launch type
    * EC2 does tasks like send container logs to CloudWatch, pull image from ECR under this profile
