* CloudTrail vs CloudWatch
    * CloudTrail to log API and user activity within SageMaker and save as S3 object
    * CloudWatch to log, monitor and alarm on invocation of endpoints and health of nodes

## CloudWatch

* CloudWatch metrics : variable to monitor including CPUUtilization, NetworkInOut etc
    * Metrics belong to namespaces
    * Dimension is an attribute of a metric
    * using CloudWatch Metric Streams, you can set destination(ex. Kinesis Data Firehose or third party provider like Datadog) to send message with near-real time
* CloudWatch Logs: choice for collecting application log(destination can be S3, Kinesis Data Streams etc)
    * sources : ECS, Lambda , API Gateway, Route53...
    * subscription filter : filter which logs of interest and send to various destination(ex. Kinesis Data Streams)
    * cloudwatch logs insights : querying functionality
* CloudWatch Logs Agent : To create log from EC2 instances, this agent must be running on the instance
    * This means that logs from EC2 cannot be sent to CloudWatch by default.
    * Latest version is CloudWatch Unified Agent, which collects additional system-level metrics like RAM, processes etc(+ centralized configuration using SSM paramter store)
* CloudWatch Alarms : with three states(ok, insufficient data, alarm), this functionality makes alert if pre-configured threshold is met.
    * main alarm target is EC2, EC2 auto scaling, SNS, but can be created from cloudwatch log metric filter
    * by setting composite alarm, user can define more sophisticated alarm
    * status check : instance status, system status(check underlying hardware), EBS status

## X-Ray

debugging in production commonly takes three steps: test locally, add log statements everywhere to find bug and re-deploy in production -> X-Ray for the rescue by visual analysis fo our applications(visualizing traces)

requirements : your code must import the AWS x-ray sdk. Then the application sdk will capture calls to AWS services, HTTP requests, DB calls and Queue calls(SQS). Make sure that EC2 IAM role has the proper permissions and EC2 instance is running the X-Ray Daemon.

## CloudTrail

Enabled by default to get an history of events/API calls made using console, SDK, CLI and AWS services(who make which call when stuff). 

* management events : configured to log management events and read/write events.
* data events : however, events regarding data(ex. logs on S3 objects, Lambda function execution) are not logged by default, because of its high volume nature
* CloudTrail insights : if enabled, it detects unusual write activity(ex. abnormal resource provisioning)