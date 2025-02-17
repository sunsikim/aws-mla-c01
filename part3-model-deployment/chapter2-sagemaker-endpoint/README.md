# Sagemaker Inference topics

MLOps is about how do I manage the larger pipeline(the workflow that surrounds the building, testing and deployment of my ML models).

## SageMaker Pipeline

SageMaker Pipeline Service is used to chain jobs defined as SageMaker projects; for example

* Processing job -> Training job(-> Model artifact to S3) -> Processing job(for evaluation) -> register mode(model registry)

Note that Sagemaker provides integration for Kubernetes, which is suitable for integrating pre-existing Kubeflow pipeline.

* Sagemaker Operators for Kubernetes : wrapping sagemaker within kubernetes environment
* Sagemaker components for Kubeflow pipelines : integration for Kubeflow pipeline

## Deployment Services

In Sagemaker, user can either use Sagemaker Jumpstart(pre-scripted notebooks) or ModelBuilder(manual handling from code) to configure deployment setting. Then, user can choose inference mode from following list:

* real-time inference : intereactive workload with low latency requirements
* Sagemaker Serverless Inference : no infrastructure management, ideal for workload with many idle time and cold start independent
* Sagemaker inference recommender : register model on the model registry, then Sagemaker performs benchmark test automatically to determine & recommend best instance type to use.
    * instance recommendation : automatic load test(~45min)
    * endpoint recommendation : custom load test based on specify instance, traffic patterns, latency requirements, throughtput requirements(~2hours)
* autoscaling : dynamically adjust compute resources based on traffic

## Deployment Guardrail

Sagemaker provides asynchronous real-time inference endpoints for following strategies.

* Blue/Green Deployments : provides way to shift traffic to new models with options below.
    * previous(new) model running on blue(green) fleet
    * all at once : shift everything to green, terminate blue if everything's ok
    * canary : shift small portion of traffic to green, everythin's ok then terminate blue
    * linear : shift traffic in linearly spaced steps
* shadow testing : runs the new model in parallel to monitor performance without affecting live traffic
* production variants : for A/B test, Sagemaker sets sets ratio of new deployment on production with Variant Weights

## Monitoring

* SageMaker model monitor : tool to monitor deployed model's usage and performance(contains performance history, detect anomalies etc)
* SageMaker model monitor data capture : used to store request-response log data in S3

## CI/CD

* AWS CodePipeline : orchestrate the different steps to have the code automatically pushed to production
* source(ex. instance start, build success) from AWS, third-party or custom services triggers event bus -> Amazon EventBridge -> json on details of event -> example destination for action
    * EventBridge infers schema; what events as json documents in the bus are going to look like. These schemas are stored in schema registry.
    * for specific event bus(and corresponding schema), user can set specific IAM permission.